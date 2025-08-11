# Creating Yara Rules with yarGen

In the previous section, we identified a file that Loki did not flag. This means if "file 2" exists on other web servers, it could go undetected.

To address this, we need to create a Yara rule to detect this specific web shell in our environment. This is a common incident response step when an event negatively impacts an organization.

## Manual Analysis

You can manually inspect the file for unique strings to use in a Yara rule. For example, to count the number of lines in the file:

```bash
strings <file name> | wc -l
```

Example output:

```bash
cmnatic@thm-yara:~/suspicious-files/file2$ strings 1ndex.php | wc -l
3580
```

Reviewing each string manually is time-consuming and inefficient.

## Example Code Snippet

Here is a snippet from `1ndex.php`:

```javascript
if(res=='error'){
    $('.ulProgress'+ulType+i).html('( failed )');
} else {
    $('.ulRes'+ulType+i).html(res);
}
loading_stop();
},
error: function(){
    loading_stop();
    $('.ulProgress'+ulType+i).html('( failed )');
    $('.ulProgress'+ulType+i).removeClass('ulProgress'+ulType+i);
    $('.ulFilename'+ulType+i).removeClass('ulFilename'+ulType+i);
}
});
}

function ul_go(ulType){
    ulFile = (ulType=='comp') ? $('.ulFileComp') : $('.ulFileUrl');
    ulResult = (ulType=='comp') ? $('.ulCompResult') : $('.ulUrlResult');
    ulResult.html('');

    ulFile.each(function(i){
        if(((ulType=='comp') && this.files[0]) || ((ulType=='url') && (this.value!=''))){
            file = (ulType=='comp') ? this.files[0] : this.value;
            filename = (ulType=='comp') ? file.name : file.substring(file.lastIndexOf('/')+1);

            ulSaveTo = (ulType=='comp') ? $('.ulSaveToComp')[i].value : $('.ulSaveToUrl')[i].value;
            ulFilename = (ulType=='comp') ? $('.ulFilenameComp')[i].value : $('.ulFilenameUrl')[i].value;
            // ...snippet cropped for brevity...
        }
    });
}
```

## Using yarGen

Luckily, we can use **yarGen** (created by Florian Roth) to automate this process.

**yarGen** is a Yara rule generator. It creates rules from strings found in malware files, excluding strings found in goodware files using its built-in databases.

Before using yarGen, update its databases (if your system has internet access):

```bash
python3 yarGen.py --update
```

This updates the good-opcodes and good-strings databases. The process may take a few minutes.

Example output after updating:

```
Updating yarGen
cmnatic@thm-yara:~/tools/yarGen$ python3 yarGen.py --update
------------------------------------------------------------------------
                                     _____
        __ _____ _____/ ___/__ ___
     / // / _ `/ __/ (_ / -_) _ \
     \_, /\_,_/_/  \___/\__/_//_/
    /___/  Yara Rule Generator
                 Florian Roth, July 2020, Version 0.23.3

    Note: Rules have to be post-processed
    See this post for details: https://medium.com/@cyb3rops/121d29322282
------------------------------------------------------------------------
Downloading good-opcodes-part1.db from https://www.bsk-consulting.de/yargen/good-opcodes-part1.db ...
```

## Generating a Yara Rule

To generate a Yara rule for "file 2":

```bash
python3 yarGen.py -m /home/cmnatic/suspicious-files/file2 --excludegood -o /home/cmnatic/suspicious-files/file2.yar
```

**Parameters:**
- `-m`: Path to the file(s) to analyze
- `--excludegood`: Exclude strings found in legitimate software to reduce false positives
- `-o`: Output location and filename for the generated Yara rule

Example output:

```
[=] Generated 1 SIMPLE rules.
[=] All rules written to /home/cmnatic/suspicious-files/file2.yar
[+] yarGen run finished
```

## Post-Processing

Review the generated Yara rule and remove any strings that may cause false positives. For this exercise, use the rule as generated and test if Yara flags "file 2".

**Note:** Another useful tool is **yarAnalyzer** (also by Florian Roth), which helps analyze Yara rules. Consider exploring it if you plan to create your own rules.

## Further Reading

- [Write Simple, Sound Yara Rules](https://www.bsk-consulting.de/2015/02/16/write-simple-sound-yara-rules/)
- [How to Write Simple but Sound Yara Rules (Part 2)](https://www.bsk-consulting.de/2015/10/17/how-to-write-simple-but-sound-yara-rules-part-2/)
- [How to Write Simple but Sound Yara Rules (Part 3)](https://www.bsk-consulting.de/2016/04/15/how-to-write-simple-but-sound-yara-rules-part-3/)
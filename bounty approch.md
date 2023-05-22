THIS IS A GENERIC APPROCH FOR #bugbounty

- take out chaos data & gather the subdomain 

```bash
cat *.txt > {outputfilename}
```

- the below cmd will take out the **in-scope** subdomain out of the chaos db of subdomains

```bash
cat allamex.txt | grep -E '(.amexgbt.com$|.banks-sadler.com$)'
```

- you can pass the in-scope file too `remember to remove * from the scope
- the list you will get after passing **all domain file** (got via chaos) with in-scope result in the `domain file` **on which you have to work**

```bash
cat allamex.txt | grep -E '(.amexgbt.com$|.banks-sadler.com$|.meetingsexpress.com$|.meetingssource.com$|www.winnerscircleregistration.com|.ovationtravel.com$)' > domain-list.txt
```

- scan the domain file to outof-scope so that you verify 

```bash
grep -Ff  outof-scope.txt domain-list.txt
```

- cmd to segrigate in-scope sub-domain from all sub-domain

```bash
grep -Ff /home/masterbaiter/Documents/dell-txt/inscope.txt all-dell.txt >  /home/masterbaiter/bugbounty/domaintohunt.txt        
```
always remember to see wc


'Important thing is to remove duplicate domains/subdomains, use | 'short -u''


`some more way to find subdomain`

```bash 
dnsx -d google.com -w /usr/share/wordlist/amass/subdomain~
```
'target :   ubisoft'
```bash
subfinder -d ubisoft.com -all (use all api) -cs (collect all resources) | tee -a finderdomain.txt (it will display result + save it as output)        
```
'while autoscaning to tools are at work do manual work like like gorking:'
```bash 
site:ubisoft.com+inurl:php|dev|registration|admin|..etc
```

#### naabu port scan on domain-list [naabu | rustscan | amass]

**Always scan ports on root**

- here people scan ports on domain (in-scope) like www.dell-technology.com, i prefer scanning on list of sub-domains

```bash
 sudo naabu -top-ports 1000 -l domain-list.txt -o nabbu-domain-list-out.txt
```

```bash
cat nabbu-domain-list-out.txt | wc -l
```


- now search for ports web on non web use **HTTPX**

```bash
httpx -l nabbu-domain-list-out.txt -title -web-server -tech-detect -location -sc -probe -v --json -o http-ports.json
```

```bash
jp -r '.input' http-ports.json
```
- now bifurcate the port on the basis of non web and web port

> **non web**
```bash
jq -r 'select(.failed == true) | .input' http-ports.json
```

> **web**
```bash
jq -r 'select(.failed == false) | .input' http-ports.json
```

note you have to make these commands according to senario/scan output
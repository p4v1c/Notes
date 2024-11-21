# Boîte noire externe

- Reconnaissance tout-en-un
```bash
# Récupérer le fichier dans le Keepass technique "Fichier de config de bbot avec clés d'API" et le placer dans ~/.config/bbot/secrets.yml
bbot -f subdomain-enum email-enum cloud-enum web-basic -m dnszonetransfer nmap gowitness git -t <DOMAIN>
```
- Ouvrir le Gowitness de bbot
```bash
docker pull leonjza/gowitness
cd ~/.bbot/scans/<ID du scan bbot>/gowitness
firefox http://localhost:7171 & docker run --rm -v $(pwd):/data -p7171:7171 leonjza/gowitness gowitness report serve --address :7171
```
- Découverte de sous-domaines
```bash
knockpy -d <DOMAINE>
python sublist3r.py -b -v -d <DOMAINE>
```
- Recherche d'adresses email
```bash
python3 EmailHarvester.py -e all -d <DOMAINE> 
```
- Fuites d'identifiants sur Dehashed
```bash
curl 'https://api.dehashed.com/search?query=email:<DOMAIN>' -u pentest@algosecure.fr:<APIKEY> -H 'Accept: application/json' | jq -r '.entries[] | [.email,.username,.password,.hashed_password,.database_name] | @csv' | sort -u
python algocreds.py -api <APIKEY> -d <DOMAIN> -bl -ex
```
- Fuite de documents sur Internet
```bash
pymeta -d <DOMAINE>
```
- Subdomain takeover
```bash
docker run -it --rm punksecurity/dnsreaper single --domain <DOMAINE>
```
- Enregistrements DNS liés à l'email
```bash
./mailsecchk.sh -d <DOMAIN>
# Pour DKIM, ne pas se fier à l'outil, mais analyser le corps des emails envoyés par l'application et le client : vérifier le paramètre d= (domaine) et s= (sélecteur)
```
- Transfert de zones
```bash
# Fonction à insérer dans le ~/.bash_aliases
domtrans(){
  dig NS $1 +short | sed -e "s/\.$//g" | while read nameserver; do echo "Testing $1 @ $nameserver"; dig "@$nameserver" axfr $1.; done
}
# Commande à lancer
domtrans <DOMAINE>
```
- Test des passerelles email
```bash
# Rappel : 25 = SMTP ; 587 = SMTPS moderne ; 465 = ancien SMTPS ; 2525 = alternative au SMTPS moderne
swaks --server <IP> -p <PORT> --from 'pentest1@algosecure.fr' --to 'pentest2@algosecure.fr' --header "Subject: Test d'intrusion AlgoSecure" --body "Bonjour, ceci est un message d'AlgoSecure. Si vous recevez cet email, votre serveur de messagerie est mal configuré. Pourriez-vous nous transmettre une copie de cet email lorsque vous le recevrez ? Merci d'avance. Cordialement."
swaks --server <IP> -p <PORT> --from '<EMAIL DU CLIENT>' --to 'pentest@algosecure.fr' --header "Subject: Test d'intrusion AlgoSecure" --body "Bonjour, ceci est un message d'AlgoSecure. Si vous recevez cet email, votre serveur de messagerie est mal configuré. Pourriez-vous nous transmettre une copie de cet email lorsque vous le recevrez ? Merci d'avance. Cordialement."
swaks --server <IP> -p <PORT> --from '<EMAIL DU CLIENT>' --to '<EMAIL D'UN AUTRE INTERLOCUTEUR CHEZ LE CLIENT>' --header "Subject: Test d'intrusion AlgoSecure" --body "Bonjour, ceci est un message d'AlgoSecure. Si vous recevez cet email, votre serveur de messagerie est mal configuré. Pourriez-vous nous transmettre une copie de cet email lorsque vous le recevrez ? Merci d'avance. Cordialement."
# Bien penser à ajouter le flag --tls sur les ports 465, 587 et 2525
```

# Boîte noire appli web

- Scans de ports standard
```bash
nmap -n -Pn --open --vv -oA <FICHIER DE SORTIE> <CIBLE>
nmap -n -Pn --open --vv -sU --defeat-icmp-ratelimit -p 53,67,68,123,135,161,162,500,1194,1494,1604,1723,2512,2513,2598,3702,4500,5000,5060,5353,5555,5566,8082,10161,10162,27000 -oA <FICHIER DE SORTIE> <CIBLE>
```
- Analyse du paramétrage TLS
```bash
./testssl.sh --html --csv <URL ou IP>
```
- Analyse du paramétrage SSH
```bash
docker pull mozilla/ssh_scan
docker run -it mozilla/ssh_scan /app/bin/ssh_scan -t <IP ou IP:port>
```
- Scan de vulnérabilités automatisé
```bash
nuclei -u <URL>
```
- Identification des composants techniques
```bash
whatweb <URL> -a 3
```
- Ouvrir la console développeur du navigateur et observer les entêtes de sécurité de la réponse, ainsi que dans les balises `<meta>` de la page
- Vérifier dans le code source de la page si les balises `<script>` possèdent l'attribut integrity (Sub-Resource Integrity) pour des ressources chargées dpeuis un serveur externe (CDN)
- Directory listing non-authentifié
```bash
gobuster dir -t 5 --delay 200ms -w /usr/share/wordlists/dirb/big.txt -o gobuster-dirb-big-unauth.log -u <URL>
gobuster dir -t 5 --delay 200ms -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-files.txt -o gobuster-raft-large-unauth.log -u <URL>
```
- Vérifier s'il y a des chemins intéressants dans le fichier robots.txt
- Injection SQL
```bash
# Sur une requête enregistrée dans un fichier texte
sqlmap --random-agent -r requete.req
# Sur un export de l'historique de proxy de Burp (Ctrl+A, clic droit, Save items)
sqlmap --random-agent -l burp.history
```
- Bruteforce
```bash
# SSH
hydra -L users.lst -P passwords.lst -t 4 ssh://<IP> # -s pour spécifier un port custom
```

# Boîte grise

- (OTG-SESS-001) Mécanisme de gestion des sessions
```
- Tenter d'insérer des caractères spéciaux dans la valeur du cookie de session
- Observer s'il est possible de forger nous-même les cookies (ex : cookie ROLE à la valeur "Admin"...)
```
- (OTG-SESS-002) Attributs des cookies
```
Ouvrir la console développeur du navigateur et observer :
- les mécanismes de sécurité des cookies
- la date de validité des cookies
```
- (OTG-SESS-003) Fixation de session
```
Si un cookie de session a été émis lors de l'arrivée sur l'application (en boîte noire, non authentifié) :
- vérifier que la valeur de ce cookie change après l'authentification
- vérifier que la valeur du cookie ne peut pas être modifiée par une requête GET ou POST
```
- (OTG-SESS-004) Variables de session exposées
```
- Observer si des requêtes sensibles s'effectuent en GET
- Tester s'il est possible de jouer des requêtes POST en GET, en changeant le type de requête et en mettant tous les paramètres dans l'URL
```
- (OTG-SESS-005) Cross-Site Request Forgery
```
- Si la session est maintenue par cookie protégé par cookie dont l'attribut SameSite est réglé à Strict, ou par entête Authorization Bearer, l'application n'est pas vulnérable aux CSRF
- Sinon, voir s'il est possible de rejouer des requêtes sensibles, c'est à dire qu'aucun mécanisme anti-rejeu ne protège ces requêtes
- Se référer à ce schéma pour déterminer le cas : https://kernelpanic.cryptid.fr/images/c/1/c/4/4/c1c44ca0d1115569284c531b26059a45563323b1-csrf.png
```
- (OTG-SESS-006) Fonction de déconnexion
```
- Vérifier que l'application propose un bouton de déconnexion
- Enregistrer une requête authentifiée, vérifier qu'on ne peut pas la rejouer après la déconnexion
```
- (OTG-SESS-007) Timeout de session
```
Si l'application est sensible du fait des données stockées (DCP ou données métier stratégiques), enregistrer une requête authentifiée, vérifier qu'on ne peut pas la rejouer après quelques heures.
```
- (OTG-SESS-008) Session incohérente
```
Vérifier qu'après avoir demandé la réinitialisation de son mot de passe, mais avant d'avoir suivi la procédure reçue par mail, on ne peut pas exécuter des requêtes authentifiées sur l'application
```
- Directory listing authentifié
```
gobuster dir -t 5 --delay 200ms -w /usr/share/wordlists/dirb/big.txt -o gobuster-dirb-big-auth.log -u <URL> -c "Cookies de session"
```
- Mécanismes de redirection contrôlée par l’utilisateur
```
À la fin de l'audit, filtrer l'historique de requêtes HTTP de Burp sur les codes 3xx [redirection], et pour chacune, observer si l'URL de redirection est contrôlée par un paramètre envoyé dans la requête
```

- Autorize burp
- Muti-account Container : https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/

- Calcul Score CVSS : https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator
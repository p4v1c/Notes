- Open relay : 
```sh
nmap <IP> -p25 --script smtp-open-relay -v
```

- try to send mail : 
```sh
swaks --from '<EMAIL>' --to '<EMAIL>' --server <IP> --header "Subject: Test d'intrusion AlgoSecure" --body "Bonjour, ceci est un message d'AlgoSecure. Si vous recevez cet email, votre serveur de messagerie est mal configuré. Pourriez-vous nous transmettre une copie de cet email lorsque vous le recevrez ? Merci d'avance. Cordialement."
```
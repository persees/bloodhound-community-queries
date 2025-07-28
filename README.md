# bloodhound-community-queries
A repo with updated queries for the new BloodHound Community edition
Some of the queries are based on the https://hausec.com/2019/09/09/bloodhound-cypher-cheatsheet/, hence the reused names.

# Cipher queries
## Find All edges any owned user has on a computer
```cipher
MATCH p=shortestPath((m:User)-[r]->(b:Computer))
WHERE coalesce(m.system_tags, '') contains 'owned'
return p
```

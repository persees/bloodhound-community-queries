# bloodhound-community-queries
A repo with updated queries for the new BloodHound Community edition.
<br>
<br>
- Some of the queries are based on the https://hausec.com/2019/09/09/bloodhound-cypher-cheatsheet/, hence the reused names.

# Cipher queries
## :green_circle: Find All edges any owned user has on a computer
```cipher
MATCH p=shortestPath((u:User)-[r]->(c:Computer))
WHERE coalesce(u.system_tags, '') contains 'owned'
return p
```

## :green_circle: Domain Users that can RDP to a workstation
```
match p=(g:Group)-[:CanRDP|AdminTo|memberOf*1..]->(c:Computer) where g.objectid ENDS WITH '-513'
AND NOT c.operatingsystem CONTAINS 'Server'
return p
```

## :green_circle: Domain Users that can RDP to a server
```
match p=(g:Group)-[:CanRDP|AdminTo|memberOf*1..]->(c:Computer) where g.objectid ENDS WITH '-513'
and c.operatingsystem CONTAINS 'Server'
return p
```

## :green_circle: Shortest paths to Domain Admins
- Normally the default query of "Shortests path to Domain Admins" already include all these queries in one. However, for organizations that have a big Active Directory environment, you may want to enumerate smaller paths to domain, computers first, then users, OUs, etc

### :small_blue_diamond: Shortest paths to Domain Admins from Computers
```
MATCH p=shortestPath((s:Computer)-[:Owns|GenericAll|GenericWrite|WriteOwner|WriteDacl|MemberOf|ForceChangePassword|AllExtendedRights|AddMember|HasSession|AllowedToDelegate|CoerceToTGT|AllowedToAct|AdminTo|CanPSRemote|CanRDP|ExecuteDCOM|HasSIDHistory|AddSelf|DCSync|ReadLAPSPassword|ReadGMSAPassword|DumpSMSAPassword|SQLAdmin|AddAllowedToAct|WriteSPN|AddKeyCredentialLink|SyncLAPSPassword|WriteAccountRestrictions|WriteGPLink|GoldenCert|ADCSESC1|ADCSESC3|ADCSESC4|ADCSESC6a|ADCSESC6b|ADCSESC9a|ADCSESC9b|ADCSESC10a|ADCSESC10b|ADCSESC13|SyncedToEntraUser|CoerceAndRelayNTLMToSMB|CoerceAndRelayNTLMToADCS|WriteOwnerLimitedRights|OwnsLimitedRights|CoerceAndRelayNTLMToLDAP|CoerceAndRelayNTLMToLDAPS|ContainsIdentity|PropagatesACEsTo|GPOAppliesTo|CanApplyGPO|HasTrustKeys|DCFor|SameForestTrust|SpoofSIDHistory|AbuseTGTDelegation*1..]->(t:Group))
WHERE t.objectid ENDS WITH '-512'
RETURN p
LIMIT 1000
```
### :small_blue_diamond: Shortest paths to Domain Admins from Users
```
MATCH p=shortestPath((u:User)-[:Owns|GenericAll|GenericWrite|WriteOwner|WriteDacl|MemberOf|ForceChangePassword|AllExtendedRights|AddMember|HasSession|AllowedToDelegate|CoerceToTGT|AllowedToAct|AdminTo|CanPSRemote|CanRDP|ExecuteDCOM|HasSIDHistory|AddSelf|DCSync|ReadLAPSPassword|ReadGMSAPassword|DumpSMSAPassword|SQLAdmin|AddAllowedToAct|WriteSPN|AddKeyCredentialLink|SyncLAPSPassword|WriteAccountRestrictions|WriteGPLink|GoldenCert|ADCSESC1|ADCSESC3|ADCSESC4|ADCSESC6a|ADCSESC6b|ADCSESC9a|ADCSESC9b|ADCSESC10a|ADCSESC10b|ADCSESC13|SyncedToEntraUser|CoerceAndRelayNTLMToSMB|CoerceAndRelayNTLMToADCS|WriteOwnerLimitedRights|OwnsLimitedRights|CoerceAndRelayNTLMToLDAP|CoerceAndRelayNTLMToLDAPS|ContainsIdentity|PropagatesACEsTo|GPOAppliesTo|CanApplyGPO|HasTrustKeys|DCFor|SameForestTrust|SpoofSIDHistory|AbuseTGTDelegation*1..]->(g:Group))
WHERE g.objectid ENDS WITH '-512'
RETURN p
LIMIT 1000
```
### :small_blue_diamond: Shortest paths to Domain Admins from Groups
```
MATCH p=shortestPath((t:Group)-[:Owns|GenericAll|GenericWrite|WriteOwner|WriteDacl|MemberOf|ForceChangePassword|AllExtendedRights|AddMember|HasSession|AllowedToDelegate|CoerceToTGT|AllowedToAct|AdminTo|CanPSRemote|CanRDP|ExecuteDCOM|HasSIDHistory|AddSelf|DCSync|ReadLAPSPassword|ReadGMSAPassword|DumpSMSAPassword|SQLAdmin|AddAllowedToAct|WriteSPN|AddKeyCredentialLink|SyncLAPSPassword|WriteAccountRestrictions|WriteGPLink|GoldenCert|ADCSESC1|ADCSESC3|ADCSESC4|ADCSESC6a|ADCSESC6b|ADCSESC9a|ADCSESC9b|ADCSESC10a|ADCSESC10b|ADCSESC13|SyncedToEntraUser|CoerceAndRelayNTLMToSMB|CoerceAndRelayNTLMToADCS|WriteOwnerLimitedRights|OwnsLimitedRights|CoerceAndRelayNTLMToLDAP|CoerceAndRelayNTLMToLDAPS|ContainsIdentity|PropagatesACEsTo|GPOAppliesTo|CanApplyGPO|HasTrustKeys|DCFor|SameForestTrust|SpoofSIDHistory|AbuseTGTDelegation*1..]->(g:Group))
WHERE g.objectid ENDS WITH '-512' and t<>g
RETURN p
LIMIT 1000
```

## :green_circle: Find Unconstrained delegation computers
```
match (c:Computer)
where c.unconstraineddelegation = true
return c
```

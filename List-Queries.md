# List Computers with Total Administrator Counts Decending
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name as Computer_Name,COUNT(DISTINCT(users)) as User_Count
ORDER BY User_Count DESC
```
# List Computer, User, and Group Permitting Administrator Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
```
# List Computers with Total RDP Counts Descending
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name as Computer_Name,COUNT(DISTINCT(users)) as User_Count
ORDER BY User_Count DESC
```
# List Computer, User, and Group Permitting RDP Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
```
# List of Objects with Interesting Rights to Domain Controllers
```
MATCH (c:Computer)-[r1:MemberOf]-(g1:Group) WHERE g1.name =~ "(?i)DOMAIN CONTROLLERS@.*"
WITH c AS DCs
MATCH (o)-[r2:Owns|:CanRDP|:AdminTo|:AddMember|:WriteDacl|:GenericAll|:GetChanges|:HasSession|:WriteOwner|:ExecuteDCOM|:AllowedToAct|:GenericWrite|:GetChangesAll|:AllExtendedRights|:AllExtendedRights|:AllowedToDelegate|:ForceChangePassword]->(DCs)
RETURN o.name AS Name, o.objectid AS SID, type(r2) AS RelationshipType, DCs.name AS DomainController
```

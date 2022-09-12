# adding_multiple_rules_with_curl
Automating the rule creation on WANdisco Fusion (LDP) 2.15.2.1-3056

## Brief explanation.
You create a list of paths, then create a script to read values from it and make an API call. 

## Basic info.
API port: 8082 or 8084 for SSL

Endpoint: /fusion/fs/deployReplicatedRule

Parameters:
```
<deployReplicatedDirectory>
<replicatedPathName>RULENAME</replicatedPathName>
<mappings>
<mapping><zoneId>1stFUSIONZONENAME</zoneId><location>PATHONYOURFILESYSTEM</location></mapping>
<mapping><zoneId>2ndFUSIONZONENAME</zoneId><location>PATHONYOURFILESYSTEM</location></mapping>
</mappings>
<preferredZone>1stFUSIONZONENAME</preferredZone>
</deployReplicatedDirectory>
```

## Example curl command to add a rule.
You can use curl in numerous ways, to add a rule we can supply curl with a file containing the paramaters or we can simply have them in the executed command. See below. 

With a file. 
```
curl -X POST -d@./stateMachine.xml -H "Content-Type: application/xml" http://node1:8082/fusion/fs/deployReplicatedRule
```

Directly on the command line.
```
curl -X POST -d "<deployReplicatedDirectory><replicatedPathName>TESTRULE1</replicatedPathName><mappings><mapping><zoneId>G1</zoneId><location>/path1</location></mapping><mapping><zoneId>G2_A</zoneId><location>/path1/</location></mapping></mappings><preferredZone>G1</preferredZone></deployReplicatedDirectory>" -H "Content-Type: application/xml" http://node1:8082/fusion/fs/deployReplicatedRule
```

## Making multiple calls to add multiple rules. 
The parameters of each request is up to you.
In my example I will compile a list of paths for my parameters.
With those parameters I will execute the request for each path in the list. 

e.g. folder_list.txt
```
/path1/
/path2/
/path3/
/path4/
/path5/
/path6/
/path7/
/path8/
/path9/
/path10/folderA/data_repo/
```

Make a call for each path. 
```
while read -r i; do curl -X POST -d "<deployReplicatedDirectory><replicatedPathName>TESTRULE-$i</replicatedPathName><mappings><mapping><zoneId>G1</zoneId><location>$i</location></mapping><mapping><zoneId>G2_A</zoneId><location>$i</location></mapping></mappings><preferredZone>G1</preferredZone></deployReplicatedDirectory>" -H "Content-Type: application/xml" http://node1:8082/fusion/fs/deployReplicatedRule; done < folder_list.txt
```
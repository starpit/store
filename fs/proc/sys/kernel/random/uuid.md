Allocate a [v4 UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier).

```shell
echo ${JOB_ID-$(uuidgen | tr '[:upper:]' '[:lower:]')}
```

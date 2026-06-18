# 使用方案

利用官方更新器生成数据库

GitHub Action：

```yaml
name: Build DependencyCheck DB

on:
  schedule:
    - cron: '0 2 * * *'
  workflow_dispatch:

jobs:
  build-db:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install DependencyCheck
        run: |
          wget https://github.com/dependency-check/DependencyCheck/releases/download/v12.1.3/dependency-check-12.1.3-release.zip

          unzip dependency-check-12.1.3-release.zip

      - name: Update Database
        run: |
          dependency-check/bin/dependency-check.sh \
          --updateonly \
          --data ./data

      - name: Archive
        run: |
          tar czf dependency-check-db.tar.gz data

      - uses: actions/upload-artifact@v4
        with:
          name: dependency-check-db
          path: dependency-check-db.tar.gz
```

---

这样每天都会生成：

```text
odc.mv.db
cache
known_exploited
```

---

然后你的服务器：

```powershell
curl -O https://你的github发布地址/dependency-check-db.tar.gz
```

解压：

```text
D:\dependency-check-data
```

扫描：

```bat
dependency-check.bat ^
  --noupdate ^
  -d D:\dependency-check-data ^
  --scan target
```

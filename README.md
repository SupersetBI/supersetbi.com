# Apache Superset 中文手册

```sh
cd super
docker run -it -v "$(pwd)":/super supersetx/sphinx-doc-build
cd cn-docs/docs && sh build.sh && exit
```
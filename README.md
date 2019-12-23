# Apache Superset 中文手册

```sh
cd super
# incubator-superset
# docs
docker run -it -v "$(pwd)":/super supersetx/sphinx-doc-build
cd docs/docs && sh build.sh
```
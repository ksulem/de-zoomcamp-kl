### Question 1. Knowing docker tags

```
docker --help
docker build --help
docker run --help
```

Which tag has the following text? - Automatically remove the container when it exits

```
docker run --rm
```

### Question 2. Understanding docker first run

Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash. 

```
docker run -i --entrypoint /bin/bash python:3.9
pip list
```

What is version of the package wheel ?

| Package    | Version |
| ---------- | ------- |
| pip        | 23.0.1  |
| setuptools | 58.1.0  |
| wheel      | 0.42.0  |

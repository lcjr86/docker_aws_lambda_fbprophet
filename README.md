# docker_aws_lambda_fbprophet
Creation of the docker image that emulates de AWS lambda with the fbprophet library properly installed

## Step-by-step of the ```fbprophet``` installation inside ```lambci/lambda:build-python3.6```

1. Get the AWS Lambda container image + run in the bash

- docker image: lambci/lambda:build-python3.6

Navegate to the folder that you would like to use as volume (the value that will be pass by '$PWD')

```
docker run --rm -it -v "$PWD":/var/task lambci/lambda:build-python3.6 bash
```

2. Inside the container:

Create the python virtual environment;

```
python3 -m venv venv
```

Activate the Virtual Environment ```venv```

```
. venv/bin/activate
```

Upgrade ```pip```

```
pip install --upgrade pip
```

Install ```pystan 2.18```

This step is important because the compatibility that's need with the gcc from the linux inside of the ```lambci/lambda:build-python3.6```

More details [here](https://github.com/facebook/prophet/issues/1057).

```
pip install pystan==2.18
```

Install ```fbprophet```

```
pip install fbprophet --no-cache
```

Reduce the ```venv``` size doing:

```
find "$VIRTUAL_ENV/lib/python3.6/site-packages" -name "test" | xargs rm -rf

find "$VIRTUAL_ENV/lib/python3.6/site-packages" -name "tests" | xargs rm -rf

rm -rf "$VIRTUAL_ENV/lib/python3.6/site-packages/pystan/stan/src"

rm -rf "$VIRTUAL_ENV/lib/python3.6/site-packages/pystan/stan/lib/stan_math/lib"
```

Check the your ```venv``` size

```
echo "venv size $(du -sh $VIRTUAL_ENV | cut -f1)"
```

Export the .zip file with the libs

```
pushd $VIRTUAL_ENV/lib/python3.6/site-packages/

bash-4.2# zip -r -9 -q /var/task/venv.zip *
```

## References

https://towardsdatascience.com/how-to-get-fbprophet-work-on-aws-lambda-c3a33a081aaf

https://github.com/facebook/prophet/issues/1057
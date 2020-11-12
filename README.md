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

zip -r -9 -q /var/task/venv.zip *
```

## Build image

1. From the terminal, go to the ```lambci_python_36_fbprophet```

2. Run: 

```
docker build --tag lambci_python_36_fbprophet .
```

You should have the image ```lambci_python_36_fbprophet```

## Get the ```venv.zip``` file (where are the libraries that you will use in your lambda)

1. On the terminal, navegate until the folder that you would like to have the .zip and run:

```
docker run --rm -it -v "$PWD":/var/task lambci_python_36_fbprophet bash
```

2. Now, that you are inside of the container, run:

```
cd $VIRTUAL_ENV/lib/python3.6/site-packages/

zip -r -9 -q /var/task/venv.zip *
```

You should be able to see the ```venv.zip``` into your local folder


## References

https://towardsdatascience.com/how-to-get-fbprophet-work-on-aws-lambda-c3a33a081aaf

https://github.com/facebook/prophet/issues/1057

https://pythonspeed.com/articles/activate-virtualenv-dockerfile/

https://ropenscilabs.github.io/r-docker-tutorial/04-Dockerhub.html
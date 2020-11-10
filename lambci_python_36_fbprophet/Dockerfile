FROM lambci/lambda:build-python3.6
ENV VIRTUAL_ENV=/opt/venv
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"
RUN pip install --upgrade pip
RUN pip install pystan==2.18
RUN pip install pandas
RUN pip install convertdate
RUN pip install lunarcalendar
RUN pip install holidays
RUN pip install tqdm
RUN pip install fbprophet --no-cache
RUN pip uninstall matplotlib -y
RUN find "$VIRTUAL_ENV/lib/python3.6/site-packages" -name "test" | xargs rm -rf
RUN find "$VIRTUAL_ENV/lib/python3.6/site-packages" -name "tests" | xargs rm -rf
RUN rm -rf "$VIRTUAL_ENV/lib/python3.6/site-packages/pystan/stan/src"
RUN rm -rf "$VIRTUAL_ENV/lib/python3.6/site-packages/pystan/stan/lib/stan_math/lib"
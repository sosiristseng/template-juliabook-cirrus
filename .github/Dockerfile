FROM python:3.11.3-slim as base

ENV JULIA_CI true
ENV JULIA_NUM_THREADS "auto"
# Let PythonCall use built-in python
ENV JULIA_CONDAPKG_BACKEND "Null"
ENV JULIA_PATH /usr/local/julia/
ENV JULIA_DEPOT_PATH /srv/juliapkg/
ENV PATH ${JULIA_PATH}/bin:${PATH}
COPY --from=julia:1.8.5 ${JULIA_PATH} ${JULIA_PATH}

FROM base

WORKDIR /app

# Python dependencies. e.g. matplotlib
COPY requirements.txt ./
RUN pip install --upgrade --no-cache-dir pip wheel setuptools && pip install --no-cache-dir -r requirements.txt

# Julia environment
COPY *.toml ./
COPY src/ src

RUN julia --color=yes --project="" -e 'import Pkg; Pkg.add("IJulia"); import IJulia; IJulia.installkernel("Julia", "--project=@.")' &&\
julia --color=yes --project=@. -e 'import Pkg; Pkg.instantiate(); Pkg.resolve(); Pkg.precompile()'

CMD ["jupyter-book"]

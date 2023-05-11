FROM python:3.11.3-slim

# Supress warnings from parallel
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

# Julia config
ENV JULIA_CI true
ENV JULIA_NUM_THREADS "auto"
# Let PythonCall use built-in python
ENV JULIA_CONDAPKG_BACKEND "Null"
ENV JULIA_PATH /usr/local/julia/
ENV JULIA_DEPOT_PATH /srv/juliapkg/
ENV PATH ${JULIA_PATH}/bin:${PATH}
COPY --from=julia:1.9.0 ${JULIA_PATH} ${JULIA_PATH}

# System packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends parallel && \
    rm -rf /var/lib/apt/lists/*

# Python dependencies. e.g. matplotlib
COPY requirements.txt ./
RUN pip install --upgrade --no-cache-dir pip wheel setuptools && pip install --no-cache-dir -r requirements.txt

# Julia environment
COPY Project.toml Manifest.toml ./
COPY src/ src

RUN julia --color=yes --project="" -e 'import Pkg; Pkg.add("IJulia"); Pkg.build("IJulia")' && \
    julia --color=yes --project=@. -e 'import Pkg; Pkg.instantiate(); Pkg.resolve(); Pkg.precompile()'

CMD ["jupyter-book"]

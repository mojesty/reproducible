# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM jupyter/minimal-notebook

MAINTAINER Jupyter Project <jupyter@googlegroups.com>

USER root

#this is a really, realy important command
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# libav-tools for matplotlib anim
RUN apt-get update && \
    apt-get install -y --no-install-recommends libav-tools && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

USER $NB_USER

#demooot environment and its friends
ENV name "demoroot"
ENV activate	"source activate $name"

RUN conda create  -n $name python=2.7
RUN conda 
RUN conda install psutil


# Install Python 2 packages
# Remove pyqt and qt pulled in for matplotlib since we're only ever going to
# use notebook-friendly backends in these images
RUN conda install -y -n $name \
    'nomkl' \
    'ipython=4.2*' \
    'ipywidgets=5.2*' \
    'pandas=0.19*' \
    'numexpr=2.6*' \
    'matplotlib=1.5*' \
    'scipy=0.17*' \
    'seaborn=0.7*' \
    'scikit-learn=0.17*' \
    'scikit-image=0.11*' \
    'sympy=1.0*' \
    'cython=0.23*' \
    'patsy=0.4*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.23*' \
    'bokeh=0.11*' \
    'hdf5=1.8.17' \
    'h5py=2.6*' \
    'sqlalchemy=1.0*' \
    'pyzmq' && \
    conda clean -tipsy

RUN conda update --quiet --yes conda
RUN $activate
#RUN conda install -c remenska -y root    
#RUN conda install -c remenska -y rootpy_plotting
RUN conda install  -c NLeSC root_numpy



# Activate ipywidgets extension in the environment that runs the notebook server
RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix
    
# Add shortcuts to distinguish pip for python2 and python3 envs
#RUN ln -s $CONDA_DIR/envs/python2/bin/pip $CONDA_DIR/bin/pip2 && \
#    ln -s $CONDA_DIR/bin/pip $CONDA_DIR/bin/pip3

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/
RUN MPLBACKEND=Agg $CONDA_DIR/envs/$name/bin/python -c "import matplotlib.pyplot"

# Configure ipython kernel to use matplotlib inline backend by default
RUN mkdir -p $HOME/.ipython/profile_default/startup
COPY mplimporthook.py $HOME/.ipython/profile_default/startup/

USER root

# Install Python 2 kernel spec globally to avoid permission problems when NB_UID
# switching at runtime.
RUN $CONDA_DIR/envs/python2/bin/python -m ipykernel install

USER $NB_USER 
ENV STARTSCRIPT /opt/start
ENV $pathtoroot	"~/.conda/envs/demoroot/bin/"

RUN echo "#!/bin/bash" > $STARTSCRIPT
RUN echo "$activate" >> $STARTSCRIPT
#RUN echo -e "# install kernels\npython2 -m ipykernel.kernelspec" >> $STARTSCRIPT
RUN echo -e " cd  $pathtoroot  " >> $STARTSCRIPT
RUN echo -e " source ./thisroot.sh  " >> $STARTSCRIPT
RUN echo -e " jupyter notebook  " >> $STARTSCRIPT
RUN echo -e "# launch notebook\njupyter notebook --ip=0.0.0.0 --no-browser" >> $STARTSCRIPT
RUN chmod +x $STARTSCRIPT

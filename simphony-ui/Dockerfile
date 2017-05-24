FROM simphonyproject/ubuntu-14.04-2-vncapp

LABEL eu.simphony-project.docker.ui_name="Simphony UI (OpenFOAM+LIGGGHTS)"
LABEL eu.simphony-project.docker.description="Simphony specialized UI for OpenFOAM+LIGGGHTS"

# Virtual environments for PYTHONPATH etc.
ENV SIMPHONYENV /simphony

# Notes for the command below.
# The Openfoam Ubuntu prebuilt package installs the library in
# /root/OpenFOAM/-2.3.1/platforms/linux64GccDPOpt/lib
# which is not accessible by the user, they need to be copied over

RUN apt-get update -qq \
    && apt-get install -y make git unzip \
    && git clone https://github.com/simphony/simphony-framework.git /tmp/simphony-framework \
    && cd /tmp/simphony-framework \
    && git checkout 0.3.3 \
    && make base \
    && wget http://dl.openfoam.org/ubuntu/dists/trusty/main/binary-amd64/openfoam231_0-1_amd64.deb -O /tmp/openfoam231_0-1_amd64.deb \
    && cp /tmp/openfoam231_0-1_amd64.deb /var/cache/apt/archives/ \
    && make apt-openfoam-deps apt-simphony-deps apt-lammps-deps fix-pip apt-mayavi-deps simphony-env lammps \
    && . $SIMPHONYENV/bin/activate \
    && pip install --no-cache-dir tables==3.2.3.1 \
    && make simphony-common simphony-mayavi simphony-openfoam simphony-liggghts simphony-lammps \
    && rm -rf /var/lib/apt/lists/* \
    && cp -rf /root/OpenFOAM/-2.3.1/platforms/linux64GccDPOpt/lib/* $SIMPHONYENV/lib \
    && echo ". $SIMPHONYENV/bin/activate" >> /etc/skel/.bashrc \
    && mkdir -p /etc/skel/Desktop /etc/skel/.enthought/mayavi_e3 /etc/skel/.config/openbox \
    && cd \
    && rm -rf /tmp/simphony-framework \
    && git clone https://github.com/simphony/simphony-ui /tmp/simphony-ui \
    && cd /tmp/simphony-ui/ \
    && git checkout 0.2.0 \
    && python setup.py install \
    && mkdir /etc/skel/examples \
    && cp simphony_ui/tests/fixtures/* /etc/skel/examples/ \
    && cd \
    && rm -rf /tmp/simphony-ui/ \
    && echo "(eval `cat $SIMPHONYENV/bin/activate` && openfoam_liggghts_ui) &" > /etc/skel/.config/openbox/autostart \
    && chmod 755 /etc/skel/.config/openbox/autostart

# Setup preference for loading simphony-mayavi plugin in Mayavi2
COPY container-files/preferences.ini /etc/skel/.enthought/mayavi_e3/

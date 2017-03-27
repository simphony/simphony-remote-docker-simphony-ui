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
    && git checkout 0.3.1 \
    && make deps apt-mayavi-deps venv-prepare \
    && . $SIMPHONYENV/bin/activate \
    && make simphony simphony-mayavi \
    && rm -rf /var/lib/apt/lists/* \
    && cp -rf /root/OpenFOAM/-2.3.1/platforms/linux64GccDPOpt/lib/* $SIMPHONYENV/lib \
    && echo ". $SIMPHONYENV/bin/activate" >> /etc/skel/.bashrc \
    && mkdir -p /etc/skel/Desktop /etc/skel/.enthought/mayavi_e3 /etc/skel/.config/openbox \
    && mv examples /etc/skel/Desktop/ \
    && cd \
    && rm -rf /tmp/simphony-framework \
    && echo "(eval `cat $SIMPHONYENV/bin/activate` && openfoam_liggghts_ui) &" > /etc/skel/.config/openbox/autostart \
    && chmod 755 /etc/skel/.config/openbox/autostart

# Setup preference for loading simphony-mayavi plugin in Mayavi2
COPY container-files/preferences.ini /etc/skel/.enthought/mayavi_e3/

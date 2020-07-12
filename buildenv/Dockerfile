FROM ubuntu:20.04

# Install build dependencies (and vim + picocom for editing/debugging)
RUN apt -qq update 
RUN export DEBIAN_FRONTEND="noninteractive" && apt install -y git wget flex bison gperf cmake ninja-build ccache libffi-dev libssl-dev dfu-util
RUN apt install -y python3 python3-pip python3-setuptools 
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 10 
RUN apt clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#ENV ESP_OPENOCD $ESP_TCHAIN_BASEDIR/openocd
#get debugger
#RUN wget -O $ESP_TCHAIN_BASEDIR/esp32-openocd.tar.gz \
#	https://github.com/espressif/openocd-esp32/releases/download/v0.10.0-esp32-20181105/openocd-esp32-linux64-0.10.0-esp32-20181105.tar.gz \
#	&& tar -zxf $ESP_TCHAIN_BASEDIR/esp32-openocd.tar.gz -C $ESP_TCHAIN_BASEDIR/ \
#	&& rm $ESP_TCHAIN_BASEDIR/esp32-openocd.tar.gz

# Setup IDF_PATH
RUN mkdir -p /esp

RUN cd /esp && git clone --branch v4.1-beta2 --depth 1 --recursive https://github.com/espressif/esp-idf.git && chmod -R 775 esp-idf
ENV IDF_PATH /esp/esp-idf

# Add the toolchain binaries to PATH
ENV PATH $ESP_TCHAIN_BASEDIR/xtensa-esp32-elf/bin:$ESP_TCHAIN_BASEDIR/openocd-esp32/bin:$IDF_PATH/tools:$PATH

RUN useradd -m -u 10000 -G dialout,root -s /bin/bash espdev
RUN su espdev -c $IDF_PATH/install.sh
RUN echo "#########\n# START Added by docker buid\n######" >> /home/espdev/.bashrc
RUN echo "source /esp/esp-idf/export.sh" >> /home/espdev/.bashrc
RUN echo "#########\n# END Added by docker buid\n######" >> /home/espdev/.bashrc
COPY ./vimrc /home/espdev/.vimrc

COPY ./entry.sh /bin/entry.sh 
RUN chmod +x /bin/entry.sh
# This is the directory where our project will show up
RUN mkdir -p /esp/project
WORKDIR /esp/project
ENTRYPOINT ["/bin/entry.sh"]


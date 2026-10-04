FROM ubuntu:24.04

# Install required packages
RUN apt-get update && \
    apt-get install -y ssh sudo net-tools && \
    rm -rf /var/lib/apt/lists/*

#-------------------------------------
# Create non-priv users
#-------------------------------------
RUN useradd -m -s /bin/bash john && \
    useradd -m -s /bin/bash mary

RUN echo "john:1john1" | chpasswd && \
    echo "mary:1mary1" | chpasswd

COPY --chown=john:john john/ /home/john/
COPY --chown=mary:mary mary/ /home/mary/

#-------------------------------------
# Add sudo (mary only)
#-------------------------------------
RUN usermod -aG sudo mary

USER john
WORKDIR /home/john

CMD ["/bin/bash"]

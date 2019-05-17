# Installation of H5C++

## Ubuntu 18.04 LTS

2019년 4월 13일

```sh
sudo apt install build-essential gcc-8 g++-8
wget http://h5cpp.org/download/hdf5-1.10.4.tar.gz
tar -xvzf tar -xvzf hdf5-1.10.4.tar.gz
cd hdf5-1.10.4 && ./configure --prefix=/usr/local && make -j2 && sudo make install 
wget http://h5cpp.org/download/h5cpp_1.10.4.1_amd64.deb
sudo dpkg -i h5cpp_1.10.4.1_amd64.deb
```

## 참조

- [AWS EC2 Cluster setup for Parallel HDF5 as well as serial HDF5 on Ubuntu 16.04LTS systems ](http://h5cpp.org/md__home_steven_Documents_projects_h5cpp_docs_pages_install.html)


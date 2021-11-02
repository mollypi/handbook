# Install from Source

Download and unzip the package Typically the source code you download will be compressed in a _.tar.gz or_ .tar.bz2 format. If the source code you've downloaded is in the format application.tar.gz, use the following command to uncompress it .

```text
tar xvfz application.tar.gz
```

If the source code you've downloaded is in the format application.tar.bz2, use the following command to uncompress it.

```text
tar xvfj application.tar.bz2
```

Configure Once you uncompress the source tar file, it will create a subdirectory in the name of the application. CD to this directory.

```text
cd application
```

Do a ./configure --help which will display all application specific configuration options that are available to you.

```text
./configure --help
```

In most cases, you can just do ./configure which will use all default values to perform the configuration. This will perform necessary pre-req checks. This will also generate the Makefile required for the installation.

```text
./configure
```

Make and Install Make command will use the Makefile created from the above step and create the application binary executable.

```text
make
```

Finally, do 'make install' which will install the application in the appropriate location.

```text
make install
```


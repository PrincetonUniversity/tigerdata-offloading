  - [Downloading data](#downloading-data)
      - [Checking available data](#checking-available-data)
      - [Downloading data](#downloading-data-1)
          - [Downloading the tarballs](#downloading-the-tarballs)
          - [Extracting the tarballs](#extracting-the-tarballs)
          - [Checking the integrity of the
            files](#checking-the-integrity-of-the-files)
  - [Uploading data](#uploading-data)

# Downloading data

## Checking available data

You can look at the data that is available on tigerdata by either
looking at the private web site:

<http://tigress-web.princeton.edu/~climate/tigerdata-storage/>

and look for your group's data (i.e. either `tigress-gvecchi`,
`tigress-stf` or `tigress-laure`) or by using `rclone` to list content
on tigerdata directly while on either `tigercpu` or `tigressdata`. For
instance:

``` example
rclone lsd tigerdata:/tigress-gvecchi/tigress/wenchang/MODEL_OUT/tiger1/
```

shows the directories under `tiger1` and

``` example
rclone ls tigerdata:/tigress-gvecchi/tigress/wenchang/MODEL_OUT/tiger1/Agung_PI_en
```

will show you the files under the directory.

## Downloading data

You may notice that the data is stored as multiple tarballs. This is for
two reasons:

1.  we use tarballs to preserve the original properties (e.g. creation
    and modification dates, directory structure) of the files stored.
2.  we split those tarballs to be able to download with multiple
    streams.

This means that there are two steps to getting the data back:

1.  Download the tarballs.
2.  untar them.

### Downloading the tarballs

Here is an example to download data that was originally in:

``` example
/tigress/wenchang/MODEL_OUT/tiger1/Agung_PI_en
```

It is recommended to download this data somewhere on `/scratch/gpfs`
Step 1:

  - If you are within a `tmux` or screen session you can do:

<!-- end list -->

``` example
rclone -Pl sync tigerdata:/tigress-gvecchi/tigress/wenchang/MODEL_OUT/tiger1/Agung_PI_en/ Agung_PI_en/ --transfers 8
```

the `-P` is to show you progress, and `--transfers 8` is to use 8
streams to download the data. Note that this will create a directory
`Agung_PI_en` under the `tmp` directory. If you use `nohup`, you don't
need to use the `-P`

``` example
nohup bash -c "/usr/bin/time -o rclone.out rclone sync -l tigerdata:/tigress-gvecchi/tigress/wenchang/MODEL_OUT/tiger1/Agung_PI_en/ Agung_PI_en/ --transfers 8" > nohup.out 2>&1 &
```

### Extracting the tarballs

One the tarballs have been downloaded you can untar them with following
on the example above:

  - if you are within a `tmux` or `screen` session

<!-- end list -->

``` example
cd Agung_PI_en
cat Agung_PI_en.tar.* | tar xf -
```

  - with `nohup`
    
    ``` example
    nohup bash -c '/usr/bin/time -o untar-out cat Agung_PI_en.tar.* | tar xf -' 1> nohup.out 2>&1 &
    ```

Once the tarballs have been extracted, you can delete them with:

``` example
rm Agung_PI_en.tar.* 
```

### Checking the integrity of the files

If the download didn't output an error, it's very likely that the files
that you downloaded are the correct. But if you have a doubt, you can
check that the files are the same as the original ones. This is done by
comparing the MD5 hash of the current file with the original one. You
can do it for:

  - individual files: this is quick,
  - the entire data that was downloaded: this is computationally
    expensive and you should avoid it unless you really need to.

<!-- end list -->

1.  Checking the md5sum of individual files
    
    The downloaded data contains a file with the `.md5` extension. It
    contains the md5 hash of every file that was downloaded as well of
    files that were symlink'ed. To continue on the example from above,
    that file would be:
    
    ``` example
    Agung_PI_en.md5
    ```
    
    Let's assume you want to compare the md5 checksum of the file
    currently
    
    ``` example
    en15/POSTP_deflate1/19630101.ocean_scalar.nc
    ```
    
    with the original md5 checksum. You get the checksum of the current
    file with:
    
    ``` example
    $ md5sum Agung_PI_en/en15/POSTP_deflate1/19630101.ocean_scalar.nc
    9d32005c7e75413844ee3d731f6df14f  Agung_PI_en/en15/POSTP_deflate1/19630101.ocean_scalar.nc
    ```
    
    and get the original one with:
    
    ``` example
    $ grep en15/POSTP_deflate1/19630101.ocean_scalar.nc Agung_PI_en.md5
    9d32005c7e75413844ee3d731f6df14f  ./en15/POSTP_deflate1/19630101.ocean_scalar.nc
    ```
    
    and compare the resulting strings. In this example the strings are
    the same.

2.  Checking the of the entire set of downloaded files
    
    To check the integrity of the entire data downloaded you can use:
    
    ``` example
    md5sum -c ../Agung_PI_en.md5
    ```
    
    This will take close to 3 hours on a set of 2 TB of data with 2100
    files.

# Uploading data

To upload data from to `tigerdata` you need to send an email to David
Luet with the subject: `tiger offload` and the list of directories that
you want uploaded in the body of the email.

  - [Downloading data](#downloading-data)
      - [Checking available data](#checking-available-data)
      - [Downloading data](#downloading-data-1)
          - [Downloading the tarballs](#downloading-the-tarballs)
          - [Extracting the tarballs](#extracting-the-tarballs)
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

will show you the files under the this directory.

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
rclone -Pl sync tigerdata:/tigress-gvecchi/tigress/wenchang/MODEL_OUT/tiger1/AgungPI_en/ Agung_PI_en/ --transfers 8
```

the `-P` is to show you progress, and `--transfers 8` is to use 8
streams to download the data. Note that this will create a directory
`AgungPI_en` under the `tmp` directory. If you use `nohup`, you don't
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

# Uploading data

To upload data from to `tigerdata` you need to send an email to David
Luet with the subject: `tiger offload` and the list of directories that
you want uploaded in the body of the email.

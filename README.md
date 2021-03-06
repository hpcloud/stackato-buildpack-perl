# Stackato Perl Buildpack

`stackato-buildpack-perl` is an [ActiveState][] buildpack for running
Perl-based applications on [Stackato][] version 3 and higher.

It uses [ActivePerl][] 5.16 already included in the default Alsek
stack. Dependencies can be installed with [Carton][] or [PPM][].

## Usage

To use this buildpack specify the URI of the repository when pushing
an application to Stackato:

```bash
stackato push <APPNAME> -n --buildpack https://github.com/ActiveState/stackato-buildpack-perl.git
```

At least one of the following files must exist to identify your app as
a Perl application:

| Filename            | Usage |
| ------------------- | ----- |
| `app.psgi`          | Indicates this a Perl application. Only needed when none of the other files exist. |
| `cpanfile.snapshot` | Install dependencies with `carton --deployment`. |
| `cpanfile`          |  Ignored if `cpanfile.snapshot` exists as well.  Otherwise install dependencies with `carton`. |
| `requirements.ppm`  |  A simple text file listing required PPM package names; installed with `ppm`. |

The `--cached` option will be added to the `carton` install commands
if dependencies have been [bundled][] with the application in the
`vendor/cache/` directory,

Specifying module requirements inside the [stackato.yml][] file is not
supported by this buildpack.  That feature is only implemented in the
[legacy buildpack][] for backwards compatibility with Stackato 2.

PPM and Carton can be used together, but Carton will not "see" any
modules installed by PPM, so might install them again.

If your application contains an `app.psgi` file, then this buildpack
will generate a default start command using [uWSGI][] as the [PSGI
server][].

## Log files

The Carton and PPM log files are stored with the generated droplet and
can be inspected with:

```bash
$ stackato files . logs/cpanm.log
$ stackato files . logs/ppm4.log
```

## Examples

A minimal example could consist of just a single `app.psgi` file:

```perl
sub {
  return ['200', ['Content-Type' => 'text/plain'], ['Hello World']];
};
```

More functional samples can be found under the [Stackato-Apps][] organization on Github:

| Sample App | Details |
| ---------- | ------- |
| [mojo-scalingdemo][] | Mojolicious app; uses Carton to install modules. |
| [perlcritic.com][] | Installs prerequisites via PPM; uses Plack as PSGI server. |

## License

This buildpack is released under version 2.0 of the [Apache License][].

[Apache License]:   http://www.apache.org/licenses/LICENSE-2.0
[ActivePerl]:       http://www.activestate.com/activeperl
[ActiveState]:      http://www.activestate.com
[bundled]:          https://metacpan.org/pod/Carton#Bundling-modules
[Carton]:           https://metacpan.org/pod/Carton
[legacy buildpack]: http://docs.stackato.com/user/deploy/buildpack.html#legacy-buildpack
[mojo-scalingdemo]: https://github.com/Stackato-Apps/mojo-scalingdemo
[perlcritic.com]:   https://github.com/Stackato-Apps/perlcritic.com
[Stackato]:         http://www.activestate.com/stackato
[Stackato-Apps]:    https://github.com/Stackato-Apps
[stackato.yml]:     http://docs.stackato.com/2.10/deploy/languages/perl/index.html#cpan-modules-via-ppm
[PPM]:              http://code.activestate.com/ppm/
[PSGI server]:      http://plackperl.org/#servers
[uWSGI]:            http://uwsgi-docs.readthedocs.org/en/latest/

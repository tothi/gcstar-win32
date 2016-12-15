# quick note about running GCstar (collection manager) on Win32 (x64)

[GCstar homepage](http://www.gcstar.org/index.en.php)

[project](https://gna.org/projects/gcstar) is hosted on [Gna!](https://gna.org)

current version (at the time of writing) is 1.7.1

win32 full-featured installer is available up to [1.6.1](http://download.gna.org/gcstar/win32/GCstar_1.6.1_Setup.exe)

requirements for running without the bundle installer:

* Perl for Windows (e.g. Strawberry Perl)
* GTK+ for Windows runtime (e.g. http://gtk-win.sourceforge.net/home/index.php/Main/Downloads)
* mandatory: Perl-Gtk2 (e.g. precompiled, instructions here: http://linux.campacasa.eu/Perl-Gtk2_on_Windows)
* optional: various Perl modules (can be compiled and installed from CPAN in case of Strawberry Perl)

once installed and can run, packaging as an executable is possible (by using patched packages/win32/createExe.bat).

## details

### mandatory prerequisits

1. install Strawberry Perl: http://strawberryperl.com/download/5.24.0.1/strawberry-perl-5.24.0.1-64bit.msi
create symlink (as admin): `mklink c:\Perl c:\Strawberry\perl`
2. install GTK+ for Windows runtime: http://downloads.sourceforge.net/gtk-win/gtk2-runtime-2.24.10-2012-10-10-ash.exe?download
3. install precompiled Perl-Gtk2 from http://www.sisyphusion.tk/ppm/ by issuing:
```
ppm install http://www.sisyphusion.tk/ppm/Glib.ppd
ppm install http://www.sisyphusion.tk/ppm/Cairo.ppd
ppm install http://www.sisyphusion.tk/ppm/Pango.ppd
ppm install http://www.sisyphusion.tk/ppm/Gtk2.ppd
```

### installing GCstar

1. get latest release (1.7.1 at the time of writing): http://download.gna.org/gcstar/gcstar-1.7.1.tar.gz
2. unpack tarball: `tar xzvf gcstar-1.7.1.tar.gz`
(for tar and others using [Cygwin](https://www.cygwin.com/) is recommended)
3. run perl app by cmdline:
```
perl gcstar\bin\gcstar
```
or create shortcut and in properties prepend `C:\Strawberry\perl\bin\perl.exe` in target field.

### optional requirements (extra perl modules for full functionality):

note, that the perl script `install` shows missing modules.
installing those modules by cpan seems to be straightforward:
```
cpan install Date::Calc
cpan install DateTime::Format::Strptime
cpan install GD::Graph::area
cpan install GD::Graph::bars
cpan install GD::Graph::pie
cpan install GD::Text
cpan install MP3::Info
cpan install MP3::Tag
cpan install Net::FreeDB
cpan install Ogg::Vorbis::Header::PurePerl
```

unfortunately the last two install cmds failed (probably not compatible with the installed Perl version).

### packaging GCstar as a standalone exe

install Perl Packer (`pp`):
```
cpan install PAR::Packer
```

takes very long time, and probably needs user interaction (closing a color selector dialog at a test).

go into gcstar\bin directory and issue (note that the two modules failed to install above are omitted):
```
pp -o gcstar.exe -g -B ^
  -M filetest ^
  -M Glib::Object::Subclass ^
  -M locale ^
  -M Digest::MD5 ^
  -M MIME::Base64 ^
  -M LWP ^
  -M LWP::Simple ^
  -M LWP::UserAgent ^
  -M Net::SMTP ^
  -M URI::Escape ^
  -M HTTP::Cookies::Netscape ^
  -M HTML::Entities ^
  -M Gtk2::SimpleList ^
  -M Text::Wrap ^
  -M XML::Simple ^
  -M MP3::Info ^
  -M MP3::Tag ^
  -M DateTime::Format::Strptime ^
  -M Archive::Tar ^
  -M Archive::Zip ^
  -M Compress::Zlib ^
  -M Time::Piece ^
  -M Getopt::Long ^
  -M FindBin ^
  -M Gtk2::Gdk::Keysyms ^
  -M Unicode::Normalize ^
  -M GD ^
  -M GD::Text ^
  -M GD::Graph::bars ^
  -M GD::Graph::area ^
  -M GD::Graph::pie ^
  -M Date::Calc ^
  -a "..\lib\gcstar;lib" ^
  -a "..\share\gcstar;share" ^
  --link=C:\Perl\site\lib\auto\Cairo\Cairo.dll ^
  --link=C:\Perl\site\lib\auto\Glib\Glib.dll ^
  --link=C:\Perl\site\lib\auto\Gtk2\Gtk2.dll ^
  -vvv ^
  gcstar
```

--> gcstar.exe standalone executable is almost ready.

changing the icon and updating version info with this perl script:
```perl
use Win32::Exe;
$exe = Win32::Exe->new('gcstar.exe');
$exe->update( icon => '../share/gcstar/icons/GCstar.ico',
              info => [ 'FileDescription=GCstar, Personal Collections Manager', 'FileVersion=1.7.1',
                        'ProductName=GCstar', 'ProductVersion=1.7.1',
                        'Comments=http://www.gcstar.org', 'LegalCopyright=GNU GPL'] );
```
```
perl update_version_info.pl
```

WARNING: do not run update_version_info.pl. Win32::Exe (or the
archive management) seems to be broken. the output exe is corrupted.

TODO: find a way to update icon and version info.

the resulting win32 (x64) binary is available in the repo for download:

[gcstar-1.7.1.exe](./gcstar-1.7.1.exe)

the binary has been assembled using:
* Windows 7 Professional x64
* Strawberry Perl 5.24.0.1
* GTK2+ for Windows runtime 2.24.10-2012-10-10-ash
* PAR Packager, version 1.035 (PAR version 1.010)

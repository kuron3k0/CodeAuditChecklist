## Command && Code Excution

- system('whoami')
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
system($xml);
```
- exec
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
print exec($xml);
```
- readpipe
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
print readpipe($xml);
```
- &#96;whoami&#96;
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
print `$xml`;
```
- qx/whoami/
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
print qx/$xml/;
```
- open
```perl
#!D:\xampp\perl\bin\perl.exe
use CGI;
my $q = CGI->new;
my $xml = $q->param( 'cmd' );

print "Content-Type: text/html\n\n";
print open(FD, "|dir $xml"); # 或者print open(FD, "dir $xml|")，需要有竖线在最前面或最后面
```
- eval
```perl
eval "system('calc')";
```
- evalbytes
```perl
use feature "evalbytes";
evalbytes "system('calc')";
```
- Template
```perl
[% template.new({'BLOCK'=>'print `whoami`;'}) %]
[% component.new({'BLOCK'=>'print `whoami`;'}) %]

当EVAL_PERL开启时可以用这两个
[% PERL %]
   $stash->set(foo => 'bar');
   print "foo value: ", $stash->get('foo');
[% END %]
[% RAWPERL %]
   $output .= "Some output\n";
   ...
   $output .= "Some more output\n";
[% END %]
```
- IPC::Run
```perl
use IPC::Run qw( run  );
run \@cat, '<', $cmd, \&out, \&err or die "cat: $?";
```
- IPC::Run3
```perl
use IPC::Run3;
run3 \@cmd, \$in, \$out, \$err;
```
- IPC:Cmd
```perl
use IPC::Cmd qw(run);
my $buffer;
print run( command => "calc",verbose => 0,buffer  => \$buffer, timeout => 20 );
```
- IPC::Open2
```perl
use IPC::Open2;
my $pid = open2(my $chld_out, my $chld_in, 'some cmd and args');
```
- IPC::Open3
```perl
use Symbol 'gensym';
use IPC::Open3;
my $pid = open3(my $chld_in, my $chld_out, my $chld_err = gensym,
                'some cmd and args');
```

## XSS
- print
```perl
print "<img src>";
```
- printf
```perl
printf "<button>aaa</button>";
```
- say
```perl
use feature "say";
say "<iframe src></iframe>";
```

## File Include
- do
- import
- no
- package
- require
- use



## SSRF
- connect
- socket
- LWP::UserAgent
- HTTP::Request
- HTTP::Response
- WWW::
- Net::
- IO::Socket::INET



## XXE
- Image::Info
- XML::Twig
- XML::LibXML 

## File
- unlink
- readlink
- rmdir
- rename
- symlink
- link
- sysopen
- mkdir
- opendir
- readdir
- IO::File


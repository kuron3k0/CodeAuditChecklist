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
- XML::Twig
```perl
use XML::Twig;
my $twig = XML::Twig->new( expand_external_ents => 1 );
$twig->parsefile( "xxe.dtd");
$twig->print;
```

- Image::Info
```perl
use Image::Info qw(image_info dim);
 
my $info = image_info("image.svg");
if (my $error = $info->{error}) {
    die "Can't parse image info: $error\n";
}
my $title = $info->{SVG_Title};
 
my($w, $h) = dim($info);
```

- XML::LibXML
```perl
#!/usr/bin/perl -w
use XML::LibXML;
 
my $xml=<<END;
<!DOCTYPE root [ <!ENTITY ent SYSTEM "file:///etc/passwd"> ]>
<node>
    <e>&ent;</e>
</node>
END

print XML::LibXML->new()->parse_string($xml);
```

## parameter override

参考[mi1k7ea大佬的文章](https://www.mi1k7ea.com/2020/11/24/Perl%E5%9F%BA%E7%A1%80-%E4%BB%A3%E7%A0%81%E5%AE%A1%E8%AE%A1/#XSS)
- 哈希引入数组导致变量覆盖

```perl
@list = ("member", "user", "admin");

%hash = (
    "user" => "mi1k7ea",
    "password" => "666",
    "level" => @list
    );

while (($k, $v) = each %hash) {
    print "$k: $v\n";
}
```

- 数组参数导致变量覆盖

```perl
sub test {
    ($a, $b, $c) = @_;
    print "$a$b$c\n";
}
# 除了第一个输出12，其他都是123
test(1, 2);
test(1, 2, 3);
test((1, 2, 3));
test(1, (2, 3));
test(1, 2, 3, 4);
test(1, (2, 3), 4);
```



## File
- CGI.upload(其实最终还是调open)
```perl
use strict;
use Fcntl;  
use CGI;

use constant RECEIVE_FILE_PATH => '/Library/WebServer/Received';
use constant BUFFER_SIZE => 10_240;
use constant MAX_SIZE_FILE_PATH => 1024*1024*512;


my $q = new CGI();
$q->cgi_error and &error($q, 'Having error creating cgi handle: '.$q->cgi_error);
my $username = $q -> param('uname') or &error($q, 'username cannot be fetched');
my $password = $q -> param('pw') or &error($q, 'password cannot be fetched');

my $file = $q -> param('upload_file') or &error($q, 'upload file name cannot be fetched');

my $fh = $q -> upload('upload_file') or &error($q, 'cannot get the handler of uploaded file with name - '.$file);

open OUTPUT, "> ".RECEIVE_FILE_PATH."/$file";
 or &error($q, 'open OUTPUT fails');  


while(<$fh>){
  print OUTPUT $_;
}


close $fh;
close OUTPUT;
```
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


# 编写 Dropbox API 脚本

> 原文：<https://dev.to/jacoby/scripting-the-dropbox-api-8bg>

我是 [Dropbox](https://www.dropbox.com/) 的超级粉丝，已经有一段时间了。我的事情之一是拥有`bin`和`lib`目录，这样我为自己编写的许多工具在我所有的主机上都可以使用。

问题是 Dropbox 的 Linux 客户端希望存在于桌面环境中，而有些地方，比如研究集群或托管机器，没有桌面运行，Dropbox 客户端就无法工作。

但是不用担心。 [Dropbox 有一个 API](https://www.dropbox.com/developers/documentation) 。Perl 有一个库。嗯，*库*，但是我喜欢并使用 [WebService::Dropbox](https://metacpan.org/pod/WebService::Dropbox) 。你的第一步是使用[应用控制台](https://www.dropbox.com/developers/apps)并获得所有你需要的钥匙。

我把这些都放到了 YAML，这样我就可以随意共享代码，不用担心共享密钥，但是也许你喜欢 JSON 或者 INI 文件什么的。无论你做什么，确保你的东西只有你能访问，特别是如果你在共享资源上运行的话。

```
---
key: gec7xnarw6yy3w8
secret: ioq7dyn412v833m
token: u1viTaKGoFNtAi6aekV4JMMd81HTSeFOn3LN-6m6pUvI1Vd867utfHmfzMVwrO2I
note: "Not my keys, but random numbers I generated. Don't share yours either." 
```

这是我的程序。如果你想从 Dropbox 下载一个`bin`目录，你可以运行`dropbox_copy.pl -d bin`。

```
#!/home/djacoby/webserver/perl/bin/perl

# This program is used to download a Dropbox directory onto a machine
# without Dropbox tools installed

use strict;
use warnings;
use utf8;
use feature qw{ postderef say signatures state };
no warnings qw{ experimental::postderef experimental::signatures };

use Carp;
use IO::File;
use JSON;
use Getopt::Long;
use Pod::Usage;
use WebService::Dropbox;
use YAML qw{LoadFile};
use File::Path qw{make_path};

my $json = JSON->new->canonical->pretty;
my $config = config();
my $dropbox = WebService::Dropbox->new( { key => $config->{key}, } );

# Authorization
if ( $config->{token} ) {
    $dropbox->access_token( $config->{token} );
}
else {
    my $url = $dropbox->authorize;

    print "Please Access URL and press Enter: $url\n";
    print "Please Input Code: ";
    chomp( my $code = <STDIN> );
    unless ( $dropbox->token($code) ) {
        die $dropbox->error;
    }
    print "Successfully authorized.\nYour AccessToken: ",
        $dropbox->access_token, "\n";
}

if ( $config->{directory} ) {
    my $remote = '/' . $config->{directory};
    get_dir( $remote, $dropbox );
}

exit;

# get_dir() takes $remote, the directory to be copied,
# and a WebService::Dropbox object. I am on an anti-globals
# kick but otherwise would've kept that and just passed $remote

sub get_dir ( $remote, $dropbox ) {
    my $local = join '', $ENV{HOME}, '/Dropbox', $remote;
    if ( !-d $local ) { make_path($local) }
    my $result = $dropbox->list_folder($remote);
    for my $e ( $result->{entries}->@* ) {

        # if it's a folder/directory, recurse
        if ( $e->{'.tag'} eq 'folder' ) {
            my $next = $e->{path_display};
            get_dir( $next, $dropbox );
        }

        # if it's a file, we download it
        if ( $e->{'.tag'} eq 'file' ) {
            my $file = $e->{path_display};
            get_file( $file, $dropbox );
        }
    }
}

# get_file() takes $remote, the file to be copied, and a
# WebService::Dropbox object

sub get_file ( $remote, $dropbox ) {
    my $local = join '', $ENV{HOME}, '/Dropbox', $remote;
    my $fh = IO::File->new( $local, '>' );
    my $response = $dropbox->download( $remote, $fh );

    # say $json->encode($response);
}

# one-stop shop to get the Dropbox configuration and the flags.

sub config () {
    my $config_file = $ENV{HOME} . '/.dropbox.yml';
    croak 'No Config' unless -f $config_file;
    my $config = LoadFile($config_file);
    $config->{download} = 0;
    $config->{upload} = 0;
    GetOptions(
        'help' => \$config->{help},
        'man' => \$config->{man},
        'directory=s' => \$config->{directory},
    );

    pod2usage( -verbose => 2, -exitval => 1 ) if $config->{man};
    pod2usage( -verbose => 1, -exitval => 1 ) if $config->{help};
    pod2usage( -verbose => 1, -exitval => 1 )
        unless $config->{directory} =~ /\w/;
    delete $config->{help};
    delete $config->{man};
    return $config;
}

exit;

=head1 NAME

dropbox_copy.pl - Download a directory from Dropbox

=head1 SYNOPSIS

    dropbox_copy.pl -d 'Images'

=head1 DESCRIPTION

This program copies whole directories from Dropbox

=head1 OPTIONS

=over 4

=item B<-d>, B<--directory>

The name of the directory to be copied.

=item B<-h>, B<--help>

Short-form user documentation

=item B<-m>, B<--manual>

Long-form user documentation

=back

=head1 LICENSE

This is released under the Artistic
License. See L<perlartistic>.

=head1 AUTHOR

Dave Jacoby L<jacoby.david@gmail.com>

=cut 
```

[作为 GitHub 要诀](https://gist.github.com/jacoby/952fe31d334fcdc236bac96f78df2223)

我应该像这样做更好的工具，列出目录和下载文件和上传。我应该用这些东西做个回购。这将允许其他人帮助它变得更好。而且因为 Dropbox 拥有“Dropbox”这个名字，所以我应该想出另一个名字。

但是，同时，请享受。

#### 如果有什么问题或意见，我很乐意倾听。在[推特](https://twitter.com/jacobydave)或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。
#Sonus

<h3>This package was originally from</h3>
https://github.com/closca/sonus<br />
https://github.com/rafasamp/sonus

Both packages have been abandoned so I have created another copy that is now Laravel 5 compatible.

## Quick Start

### Setup

Update your `composer.json` file and add the following under the `require` key

	"volrac/sonus": "dev-master"

Run the composer update command:

	$ composer update

In your `config/app.php` add `'Volrac\Sonus\SonusServiceProvider'` to the end of the `$providers` array

    'providers' => array(

        'Illuminate\Foundation\Providers\ArtisanServiceProvider',
		    'Illuminate\Auth\AuthServiceProvider',
        ...
        'Volrac\Sonus\SonusServiceProvider',

    ),

Still under `config/app.php` add `'Sonus' => 'Volrac\Sonus\Facade'` to the `$aliases` array

    'aliases' => array(

        'App'             => 'Illuminate\Support\Facades\App',
        'Artisan'         => 'Illuminate\Support\Facades\Artisan',
        ...
        'Sonus'           => 'Volrac\Sonus\Facade',

    ),

Run the `artisan` command below to publish the configuration file

	$ php artisan vendor:publish

Navigate to `app/config/sonus.php` and update all four parameters

### Examples

Here is a simple example of a file being converted from FLAC to AAC:

	Sonus::convert()->input('foo.flac')->bitrate(128)->output('bar.aac')->go();

Sonus can also convert video files:

	Sonus::convert()->input('foo.avi')->bitrate(300, 'video')->output('bar.flv')->go();

Sonus can also return media information as an array or json

    Sonus::getMediaInfo('foo.mov');

Sonus can also easily generate smart movie thumbnails like this

    Sonus::getThumbnails('foo.mp4', 'foo-thumb', 5); // Yields 5 thumbnails

Sonus can also thumbnify your movie (create thumbs for a short preview)

    Sonus::thumbnify('foo.mp4', 'foo-thumb', 40, '400'); // Yields 40 thumbnails (every 10 seconds) and video has 400 secs

Although Sonus contains several preset parameters, you can also pass your own

	Sonus::convert()->input('foo.flac')->output('bar.mp3')->go('-b:a 64k -ac 1');

### Tracking progress

Make sure the `progress` and `tmp_dir` options are set correctly in the config.php file

    'progress'      => true,
    ...
    'tmp_dir'      => storage_path().'/sonus/tmp/'

Pass the progress method when initiating a conversion

    Sonus::convert()->input('foo.avi')->output('bar.mp3')->progress('uniqueid')->go();

Now you can write a controller action to return the progress for the job id you passed and call it using any flavor of JavaScript you like

    public function getJobProgress($id)
    {
        return Sonus::getProgress('uniqueid');
    }


### Security and Compatibility

Sonus uses PHP's [shell_exec](http://us3.php.net/shell_exec) function to perform ffmpeg and ffprobe commands. This command is disabled if you are running PHP 5.3 or below and [safe mode](http://us3.php.net/manual/en/features.safe-mode.php) is enabled.

Please make sure that ffmpeg and ffprobe are at least the following versions:

* ffmpeg: 2.1.*
* ffprobe: 2.0.*

Also, remember that filepaths must be relative to the location of FFMPEG on your system. To ensure compatibility, it is good practice to pass the full path of the input and output files. Here's an example working in Laravel:

    $file_in  = Input::file('audio')->getRealPath();
    $file_out = '\path\to\my\file.mp3'; 
    Sonus::convert()->input($file_in)->output($file_out)->go();

Lastly, Sonus will only convert to formats which ffmpeg supports. To check if your version of ffmpeg is configured to encode or decode a specific format you can run the following commands using `php artisan tinker`

    var_dump(Sonus::canEncode('mp3'));
    var_dump(Sonus::canDecode('mp3'));

To get a list of all supported formats you can run

    var_dump(Sonus::getSupportedFormats());

## Troubleshooting

Please make sure the following statements are true before opening an issue:

1) I am able to access FFMPEG on terminal using the same path I defined in the Sonus configuration file

2) I have checked the error logs for the webserver and found no FFMPEG output messages

Usually all concerns are taken care of by following these two steps. If you still find yourself having issues you can always open a trouble ticket.


## License

Sonus is free software distributed under the terms of the MIT license.

## Aditional information

Any questions, feel free to contact me.

Any issues, please [report here](https://github.com/volrac/sonus/issues)

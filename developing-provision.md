# Developing Provision

One of our missions is to be _Easy to Develop._

This is not just for the core team, but also so others can come in and customize the system to their needs without much difficulty.

## Code

The source code for provision is available at [github.com/aegir-project/provision](https://github.com/aegir-project/provision). The main branch is `4.x`.

The important files and folders are:

* composer.json  -  Defines the project and the dependencies.
* bin/provision  -  Executable. Run this to use provision.
* src/  -  All the new classes.
* vendor/  -  The new vendor directory. All composer libraries get installed here when you run `composer install`.
* .travis.yml  -  Automated tests for our new branch.
* README.md  - The main documentation file.

NOTE: The entire codebase pre-4.x is still in the repository, so \_everything else \_is legacy code.

## Classes

### [class Provision](https://github.com/aegir-project/provision/blob/4.x/src/Provision.php)

Implements:

* ConfigAwareInterface: `$provision->getConfig()`to load the CLI config, optionally loaded from ~/.provision.yml
* ContainerAwareInterface: Uses [container.thephpleague.com](http://container.thephpleague.com) for dependency injection.
* LoggerAwareInterface: `$provision->getLogger()->info()` to `$provision->getLogger()->debug(),` easy PSR logging.
* BuilderAwareInterface: Access to the Robo Builder. 
* and more... API docs coming soon.

### [class Context](https://github.com/aegir-project/provision/blob/4.x/src/Context.php)

A Context represents an object we are tracking, either a Server, Platform, or Site.

Each context type has different properties, defined in the `option_documentation()` method.

`$context->save()` will save the context properties into a YML file. Run provision status to reveal the path to a context's YML file.

`$context->verifyCommand()`is triggered when the `provision verify` command is run

### [class ServerContext](https://github.com/aegir-project/provision/blob/4.x/src/Context/ServerContext.php)

ServerContext "provide" services, while all others "subscribe" to them.

Use `ServerContext::shell_exec()` to easily run commands in the Server's config directory, while hiding output, throwing exception with error messages, and showing output when running with `-v`.

## Steps

To create the list of things that run during a provision\_verify There is a class called "Provision\Step".

An Example: 

```php
use Aegir\Provision\Step;
$steps['coinflip'] = Step::create()
    // Sets a character other than ☐ as the starting prefix.
    // Useful if you know your step will output lines and the ☐ doesn't look right.
    ->startPrefix('💲')

    // The message to display when the step starts.
    ->start('Flipping a coin...')

    // The message to display if the steps execute successfully.
    ->success('Win!')

    // The message to display if the steps fail. (The string "FAILED in 1s" will be appended).
    ->failure("Lose!")

    // The code to execute for the step.
    // This can be a callable (a function string or anonymous function)
    // Or it can be a Robo Task or Robo Collection.
    // if using a callable, must return an exit code (0 for success)
    ->execute(function (){
        $choices = ['head', 'tail'];

        $choice = $this->getProvision()->io()->choice('Heads or Tails?', $choices);
        $this->getProvision()->io()->warningLite("You chose " . $choice);

        foreach ([3,2,1] as $countdown) {
            $this->getProvision()->io()->bulletLite('Flipping a coin in ' . $countdown);
            sleep(1);
        }
        $flip = rand(0,1);
        $this->getProvision()->io()->warningLite('Coin landed on: ' . $choices[$flip]);

        return $choice == $choices[$flip]? 0: 1;
    });
```

This code results in the following output:

![](.gitbook/assets/apr-13-2018-10_21-am-edited.gif)


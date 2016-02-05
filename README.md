# nested-yargs

An opinionated wrapper around [yargs](https://npmjs.org/packages/yargs) to make
nested command-line structures a breeze.

## Key Features

* Built off of the proven command-line argument parser [yargs](https://npmjs.org/packages/yargs).
* Lets you structure your CLI tool as a set of **categories** and **commands**.
* Lets you easily re-use logic between different commands.
* Makes it easy to alias one command to another.
* Will provide intuitive help without the headache of setting it up.Key Features

## Installing it

```bash
npm install nested-yargs
```

## Using it

```js
var Cli = require('nested-yargs');
var Widgets = require('./widget-service'); // Imaginary api

// The 'root' of our app is an anonymous category
var app = Cli.createApp();

// You add categories and commands using the `.command()` method.
app.command(Cli.createCommand('init', 'Initialize the this tool.', {
    handler: function (argv) {
        // Initialize the tool
    }
});


// You can nest categories as deep as you would like
var widgets = Cli.createCategory('widgets', 'Manage your inventory of widgets.');

widgets.command(Cli.createCommand('ls', 'List your widgets.', {
    // Options follow the yarg option format
    options: {
        color: {
            alias: 'c'
            description: 'Only list widgets of the given color.',
            type: 'string',
        }
    }
    handler: function (argv) {
        Widgets.list(argv, function (err, widgets) {
            if (err) throw err; // Thrown errors will print the error in red
                                // and print the help.
            
            // Pretty-print your list of widgets however you'd like
        });
    }
}));
// The ls command is now available at `widgets.commands.ls`

// Alias `widgets ls` to the top-level command `widgets ls`
app.command(Cli.createCommand('ls', widgets.commands.ls.description,
    widgets.commands.ls.options));

app.command(widgets);

Cli.run(app);
```

## API

### Cli.createApp()

Creates a new `nested-yargs` application (actually a `Category` with its name
set to `'$0'`.

### Cli.createCategory(name, description)

Create and return new `Category` object with the given name and description.

#### Category objects

**Properties**

* `.name`
* `.description`
* `.path` - If the category is attached, this will be an array of ancestor command names
* `.parent` - If the category is attached, this is a reference to its parent category

**Methods**

* `.command(<Category|Command>)` - Register a child Category or Command

### Cli.createCommand(name, description, options)

Create and return new `Command` object with the given name, description and options.

Here, **options** supports the following properties:

* `params:` - A string describing the required and optional parameters (not options)
  Example: `"<name> [field]"`. Here name is required but field is optional.
  These values will be available to handlers as `argv.params[paramName]`.
* `setup:` - A function that will be invoked with the yargs instance if this
  command is triggered once `Cli.run()` has been called.
* `options:` - A hash of options in the yargs format as per
  [yargs docs](https://github.com/bcoe/yargs#optionskey-opt).
* `handler:` - A function that will be invoked with the parsed `argv` object.

#### Command objects

**Properties**

* `.name`
* `.description`
* `.path` - If the category is attached, this will be an array of ancestor command names
* `.parent` - If the category is attached, this is a reference to its parent category
* `.options` - The options passed in to the Command constructor

### Cli.run(<Category|Command>, [yargs])

Once you have set up your cli application's structure, it is time to run it.

## Usages

This library is used in [wt-cli](https://github.com/auth0/wt-cli).

## Contributing

Just clone the repo, run `npm install` and then hack away.

## Issue Reporting

If you have found a bug or if you have a feature request, please report them at this repository issues section. Please do not report security vulnerabilities on the public GitHub issue tracker. The [Responsible Disclosure Program](https://auth0.com/whitehat) details the procedure for disclosing security issues.

## Author

[Auth0](auth0.com)

## License

This project is licensed under the MIT license. See the [LICENSE](LICENSE) file for more info.

## What is Auth0?

Auth0 helps you to:

* Add authentication with [multiple authentication sources](https://docs.auth0.com/identityproviders), either social like **Google, Facebook, Microsoft Account, LinkedIn, GitHub, Twitter, Box, Salesforce, amont others**, or enterprise identity systems like **Windows Azure AD, Google Apps, Active Directory, ADFS or any SAML Identity Provider**.
* Add authentication through more traditional **[username/password databases](https://docs.auth0.com/mysql-connection-tutorial)**.
* Add support for **[linking different user accounts](https://docs.auth0.com/link-accounts)** with the same user.
* Support for generating signed [Json Web Tokens](https://docs.auth0.com/jwt) to call your APIs and **flow the user identity** securely.
* Analytics of how, when and where users are logging in.
* Pull data from other sources and add it to the user profile, through [JavaScript rules](https://docs.auth0.com/rules).

## Create a free account in Auth0

1. Go to [Auth0](https://auth0.com) and click Sign Up.
2. Use Google, GitHub or Microsoft Account to login.

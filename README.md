# Clicksign Ruby Client

Official gem to comunicate with the **[Clicksign REST API](http://clicksign.readme.io/)**.

## Installation

With Bundler:

```
gem 'clicksign'
```

Manual installation via RubyGems:

```shell
gem install clicksign
```

## Usage

### Setting up the client

You must provide a valid `token` in order to use the library. As an option, you can also set a different `endpoint`.

The required `token` is provided by the Clicksign support team.

```ruby
require 'clicksign'

Clicksign.configure do |config|
  config.token = ENV['CLICKSIGN_TOKEN']
  config.endpoint = 'https://api.clicksign-demo.com' # Default: 'api.clicksign.com'
end
```

### Retrieving a list of documents

You'll be able to make requests to the Clicksign API right after the initial setup. The first step would be to retrieve a list of documents that you've previously uploaded to your account.

```ruby
documents = Clicksign::Document.all
```

### Uploading a document

To upload a new document to Clicksign you can use the following snippet:

```ruby
document = Clicksign::Document.create(File.new('example.pdf'))
```

You can also upload a new document and at the same time set up a signature list
as follow:

```ruby
document = Clicksign::Document.create(File.new('example.pdf'), signers: [{ act: 'sign', email: 'john.doe@example.com' }], message: 'Please sign it', skip_email: true)
```

It is important to notice that the additional parameters to `create` method is
the same as the ones in the section [Creating a signature list](#user-content-creating-a-signature-list)


### Retrieving a document

```ruby
found = Clicksign::Document.find(document_key)
```

### Downloading a document

```ruby
zip = Clicksign::Document.download(document_key)
File.open('mydoc.zip', 'wb') { |f| f.write(zip) } if zip
```

If _found_ is _nil_, it means that the server is preparing the zip file.
When the zip is ready, its contents are retrieved.

### Creating a signature list

The method `Clicksign::Document.create_list` accepts **3 arguments**, the latter being optional.

The first argument is `document_key`, which represents the document's unique identification.

The second argument is `signers`, an array of hashes with the e-mails of the signers and their actions (`act`). The available options for `act` are described in our [documentation](http://clicksign.github.io/rest-api/#criacao-de-lista-de-assinatura).

The third and optional parameter is `skip_email`, a boolean that says whether the API should send e-mails to the signers or not.

Example:

```ruby
document_key = KEY
signers = [{ email: 'john.doe@example.com', act: 'sign' }]
result = Clicksign::Document.create_list(document, signers, true)
```

### Resending a signature request

Use the following snippet to send a email to a signer that have not signed yet:

```ruby
messsage = 'This is a reminder for you to sign the document.'
Clicksign::Document.resend(key, email, message)
```

### Canceling a document

```ruby
doc = Clicksign::Document.cancel(document_key)
```

The method returns the canceled document.

### Hooks

You can perform three different actions with hooks: **retrieve** all, **create** a new one or **delete** an existing hook.

Listing all hooks that belong to a document:

```ruby
Clicksign::Hook.all(document_key)
```

Creating a new hook for a specific document:

```ruby
Clicksign::Hook.create(document_key, 'http://example.com')
```

Destroying an existing hook:

```ruby
Clicksign::Hook.delete(document_key, hook_id)
```

### Batches

Batches are you used to group documents in a package and perform batch signatures via the [Clicksign Widget](https://github.com/clicksign/widget).

You can perform three different actions with batches: **retrieve** all, **create** a new one or **delete** an existing batch.

Listing all batches:

```ruby
Clicksign::Batch.all
```

Creating a new batch for a group of documents:

```ruby
Clicksign::Batch.create([key1, key2, key3])
```

Destroying an existing batch:

```ruby
Clicksign::Batch.delete(batch_key)
```

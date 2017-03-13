# DEPRECATED
The origin is wonderful.  
This fork is garbage.

# AdminScript

fork from [alpaca-tc/admin_script](https://github.com/alpaca-tc/admin_script)

- Rails 4 supported version(Rails 5 not supported)

- Modified to return success or failure message

powered by　[Google Translate](https://translate.google.com/)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'admin_script', github: 'freedomatbones/admin_script'
```

And then execute:

    $ bundle

## Usage

`app/controllers/backend/admin_scripts_controller.rb`:
```ruby
class Backend::AdminScriptsController < Backend::BaseController
  include ::AdminScript::Controller

  # enable "AdminScript::Base.subclasses" for development
  unless Rails.application.config.cache_classes
    Dir.glob(File.join(Rails.root, "app", "models", "admin_script", "*.rb")).each { |f| load f }
  end

  private

  # override: admin_script/lib/admin_script/controller.rb
  #           add subdirectory backend
  def admin_script_path(id)
    param = id.try(:to_param) || id
    "/backend/admin_scripts/#{param}"
  end

  # override: admin_script/lib/admin_script/controller.rb
  #           add subdirectory backend
  def admin_scripts_path
    '/backend/admin_scripts'
  end
end
```

`app/models/admin_script/{script_name}.rb`:
```ruby
class AdminScript::{ScriptName} < AdminScript::Base
  self.description = <<-EOS.strip_heredoc
    Description
  EOS

  self.type_attributes = {
    name: :string,
    price: :integer
  }

  def initialize(*args)
    self.name = 'hoge'
    self.price = 980
    super *args
  end

  def perform!
    log = []

    start_time = Time.current
    log << "start #{start_time}"

    success = []
    exceptions = []

    begin
      item = Item.create!(name: name, price: price)
      success << item.to_s
    rescue => e
      exceptions << "name: #{name}, price: #{price} => #{e.to_s}"
    end

    log << exceptions
    log << success

    exceptions.size == 0 ? {notice: log.flatten.join('<br>')} : {alert: log.flatten.join('<br>')}
  end
end
```

`config/routes.rb`:
```ruby
  namespace :backend do
    resources :admin_scripts, as: :admin_scripts
  end
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/admin_script.


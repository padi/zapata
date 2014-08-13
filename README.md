[![Code Climate](https://codeclimate.com/github/Nedomas/zapata/badges/gpa.svg)](https://codeclimate.com/github/Nedomas/zapata)
[![Gem Version](https://badge.fury.io/rb/zapata.svg)](http://badge.fury.io/rb/zapata)
[![Build Status](https://travis-ci.org/Nedomas/zapata.svg?branch=master)](https://travis-ci.org/Nedomas/zapata)

# Zapata

Who has time to write tests? This is a revolutional tool to make them write
themselves.

![Emiliano Zapata](https://cloud.githubusercontent.com/assets/1877286/3753719/af3bfec2-1814-11e4-8790-242c2b26a8e9.jpg)

# What is your problem?

There comes a day where you have this bullshit class ``RobotToTest``. We need
tests. :shipit:

```ruby
class RobotToTest
  def initialize(human_name, cv)
    @name = robot_name(human_name)
  end

  def robot_name(human_name)
    "#{self.class.prefix}_#{human_name}"
  end

  def cv
    { planets: planets }
  end

  def nested_fun_objects(fun_objects)
    'It was fun'
  end

  def self.prefix
    'Robot'
  end

  private

  def planets
    ['Mars', Human.home]
  end

  def fun_objects
    [%i(array in array), { hash: nested_hash }]
  end

  def nested_hash
    { in_hash: { in: array } }
  end

  def array
    %w(array)
  end
end

# just another class to analyze
class Human
  def initialize
    human_name = 'Emiliano'
  end

  def self.home
    'Earth'
  end
end
```

## Solving it

You run ``zapata generate app/models/robot_to_test.rb`` and pop the champagne.
Zapata generated ``spec/models/robot_to_test_spec.rb`` for you.

```ruby
describe RobotToTest do
  let(:robot_to_test) do
    RobotToTest.new('Emiliano', { planets: ['Mars', Human.home] })
  end

  it '#robot_name' do
    expect(robot_to_test.robot_name('Emiliano')).to eq('Robot_Emiliano')
  end

  it '#cv' do
    expect(robot_to_test.cv).to eq({ planets: ['Mars', 'Earth'] })
  end

  it '#nested_fun_objects' do
    expect(robot_to_test.nested_fun_objects([
      [:array, :in, :array],
      {
        hash: {
          in_hash: {
            in: ['array']
          }
        }
      }
    ])).to eq('It was fun')
  end

  it '#prefix' do
    expect(RobotToTest.prefix).to eq('Robot')
  end
end
```

## What does it do?

It tries to write a passing RSpec spec off the bat. It does fancy analysis
to predict the values it could feed to the API of a class.

__To be more specific:__
- Analyzes all vars and methods definitions in ``app/models``
- Checks what arguments does a testable method in ``app/models/robot_to_test.rb`` need
- Searches for such variable or method name in methods in analyzed
- Selects the most probable value by how many times it was repeated in code
- Runs the RSpec and fills in the expected values of the test with those returned by RSpec

For more things it can currently do check
https://github.com/Nedomas/zapata/tree/master/spec

## Requirements

- Ruby 2.0+
- Rails 3.0+

## Installation

It should be as easy as
```sh
gem install zapata
```

or

```ruby
gem 'zapata', groups: %w(development test)
```

## Usage

To use run
```sh
zapata generate app/models/model_name.rb
```

To ignore other files and analyze a single model you want to generate a spec for:
```sh
zapata generate app/models/model_name.rb --single
```

## Collaboration :heart:

It is encouraged by somehow managing to bring a cake to your house. I promise,
I will really try.

This is a great project to understand language architecture in general. A
project to let your free and expressionistic side shine through by leaving meta
hacks and rainbows everywhere.

Thank you to everyone who do. I strongly believe that this can make the
developer job less robotic and more creative.

To install, run:
```sh
git clone https://github.com/Nedomas/zapata
cd zapata
bundle exec rake install
```

For specs, run:
```sh
bundle exec rspec --patern "spec/*_spec.rb"
```

## Copyright
Copyright (c) 2014 Domas.
See [LICENSE](LICENSE) for details.

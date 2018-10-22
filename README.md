### Hashie
---

https://github.com/intridea/hashie

```
gem install hashie

```

```ruby
Hashie.logger = Rails.logger

class Tweet < Hash
end
user_hash = { name: "Bob" }
Tweet.new(user: user_hash)

class SpecialHash < Hash
  include Hashie::Extensions::Coercion
  coerce_value Hash, SpecialHash
  def initialize(hash = {})
    super
    hash.each_pair do |k,v|
      self[k] = v
    end
  end
end

class Tweet < Hash
  include Hashie::Extensions::Coercion
  coerce_key :mentions, Array[User]
  coerce_key :friends, Set[User]
end
user_hash = { name: "Bob" }
mentions_hash = [user_hash, user_hash]
friends_hash = [user_hash]
tweet = Tweet.new(mentions: mentions_hash, friends: friends_hash)
tweet.mentions.map(&:class)
tweet.friends.class

class Relation
  def initialize(string)
    @relation = string
  end
end
class Tweet < Hash
  include Hashie::Extensions::Coercion
  coerce_key :relations, Hash[User => Relation]
end
user_hash = { name: "Bob" }
relations_hash = { user_hash => "father", user_hash => "frined" }
tweet = Tweet.new(relations: relations_hash)
tweet.relations.map { |k,v| [k.class, v.class] }
tweet.relations.class 

class Tweet < Hash
  include Hashie::Extensions::Coercion
  coerce_key :retweeted, ->(v) do
    case v
    when String
      !!(v =~ /\A(true|t|yes|y|1)/i)
    when Numeric
      !v.to_i.zero?
    else
      v == true
    end
  end
end

class CategoryHash < Hash
  include Hashie::Extensions::Coercion
  include Hashie::Extensions::MergeInitializer
  
  coerce_key :products Array[ProductHash]
end
class ProductHash < Hash
  include Hashie::Extensions::Coercion
  include Hashie::Extensions::MergeInitializer
  coerce_key :categories, Array[CategoriesHash]
end
























class KeepingMash < ::Hashie::Mash
  include Hashie::Extensions::Mash::KeepOriginalKeys
end
mash = KeepingMash.new(:symbol_key => :symbol, 'string_key' => 'string')
mash.to_hash == { :symbol_key => :symbol, 'string_key' => 'string' }
mash.symbol_key
mash.[:symbol_key]
mash['symbol_key']
mash.string_key
mash['string_key']
mash[:string_key]

class SafeMash < ::Hashie::Mash
  include Hashie::Extensions::Mash::SafeAssignment
end
safe_mash = SafeMash.new
safe_mash.zip = 'Test'
safe_mash[:zip] = 'test'

class SymbolizedMash < ::Hashie::Mash
  include Hashie::Extensions::Mash::SymbolizeKeys
end
symbol_mash = SymbolizedMash.new
symbol_mash['test'] = 'value'
symbol_mash.test
symbol_mash.to_h


symbol_mash = SymbolizedMash.new(id: 123, name: 'Rey')
symbol_mash.each do |key, value|
end

class Person < Hashie::Dash
  property :name, required: true
  property :age, required: true, message: 'must be set.'
  property :email
  property :phone, required -> { email.nil? }, message: 'is ....'
  property :pants, required: :weekday?, message: 'are...'
  property :occupation, default: 'Rubyist'
  def weekday?
    [ Time.now.saturday?, Time.now.sunday? ].none?
  end
end
p = Person.new
p = Person.new(name: 'Bob')
p = Person.new(name: "Bob", age: 18)
p .name
p.name = nil
p.age
p.age = nil
p.age = nil
p.email = 'abc@def.com'
p.occupation
p.email
p[:awesome]
p[:occupation]
p.update_attributes!(name: 'Trudy', occupation: 'Evil')
p.ocupation
p.name
p.update_attributes!(occupation: nil)
p.occupation

class Tricky < Hashie::Dash
  property :trick
  property 'trick'
end
p = Tricky.new(trick: 'one', 'trick' => 'two' )
p.trick
p[:trick]
p['trick']

class Tricky < Hashie::Dash
  property 'trick'
end
p = Tricky.new('trick' => 'two')
p.trick

class Foo < Hahie::Dash
  property :bar
end
foo = Foo.new(bar: 'baz')
qux = { **foo, quux: 'corge' }
qux.is_a?(Foo)
qux.key?(:quex)

qux = { **foo, quux: 'corge' }.to_h
qux.is_a?(Hash)
qux[:quux]
qux = { quux: 'corge', **foo }
qux.is_a?(Hash)
qux[:quux]

class PersonHash < Hashie::Dash
  include Hashie::Extensions::Dash::PropertyTranslation
  property :first_name, from: :firstName
  property :last_name, from: :lastName
  property :first_name, from: :f_name
  property :last_name, from: :l_name
end
person = PersonHash.new(firstName: 'Micheal', 1_name: 'Bleigh')
person[:frist_name] 
person[:last_name]

class DataModelHash < Hashie::Dash
  include Hashie::Extensions::Dash::PropertyTranslation
  property :id, transform_with: ->(value) { value.to_i }
  property :created_at, from: :created, with: ->(value) { Time.parse(value) }
end
model = DataModelHash.new(id: '123', created: '2014-04-25 22:34:28')
model.id.class
model.created_at.class

class UserHash < Hahie::Dash
  include Hshie::Extensions::Coercion
  property :id
  property :posts
  coerce_key :posts, Array[PostHash]
end
class UserHash < Hashie::Dash
  include Hashie::Extensions::Dash::Coercion
  property :id
  property :post, coerce: Array[PostHash]
end

class Person < Hashie::Trach
  property :first_name, from: :firstName
end
Person.new(firstName: 'Bob')
class Result < Hashie::Trash
  property :id, transform_with: lambda { |v| v.to_i }
  property :created_at, from: :creation_date, with: lambda { |v| Time.parse(v) ]
end

result = Result.new(id: '123', creation_date: '2012-03-30 17:23:28')
result.id.class
result.created_at.class

c = Hashie::Clash.new
c.where(abc: 'def').order(:created_at)
c
c = Hshie::Clash.ne
c.where!.abc('def').ghi(123)._end!.order(:created_at)
c
c = Hashie::Clash.new
c.where(abc: 'def').where(hgi: 123)
c

greeting = Hshie::Rash.new(/^Mr./ => "Hello sir!", /^Mrs./ => "Evening, mandame." )
greeting["Mr. Steve Austin"]
greeting["Mrs. Steve Austin"]
mapper = Hashie::Rash.new(
  /I like (.+)/ => proc { |m| "Who DOESN'T like #{m[1]}?!" },
  /Get off my (.+)!/ => proc { |m| "Forget your #{m[1]}, old man!" }
)
mapper["I like traffic lights"]
mapper["Get off my lawn!"]

```

```
```


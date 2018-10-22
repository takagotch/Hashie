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


```

```
```


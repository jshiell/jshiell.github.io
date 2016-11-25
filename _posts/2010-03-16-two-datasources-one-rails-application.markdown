---
layout: post
title: Two Datasources, One Rails Application
---
Rails has a habit of lulling you into a false sense of security. The ease of use for common tasks makes you wonder why you’re wasting your time with Spring or JEE. Then you try to do something off the common path and watch Rails just shrug its shoulders and grin.

I was working on a community project recently and ran into the requirement to access some static data in another database. Multiple datasources, easy enough. Unfortunately, Rails expects you’ll be sticking with just one, and so I had to go hunting for a solution. Thankfully, I wasn’t the first to hit this and so many people had already done the hard work, leaving me with my piecing together the most elegant solution.

The first thing to do is define your datasource, presumably in *database.yml*.
```
legacy_datasource:
  adapter: mysql
  database: legacy_database
  timeout: 5000
  encoding: utf8
  host: localhost
  username: readonly
  enable_call: true
  password: readonly    
```
You’ll then need to introduce a base class for the models which will use this datasource. It’ll need to be abstract, to avoid declaring any columns and establish a connection to the secondary source.
```
class LegacyObject < ActiveRecord::Base
  establish_connection :legacy_datasource

  self.abstract_class = true

  def self.columns() @columns ||= []; end

  def self.column(name, sql_type = nil, default = nil, null = true)
    columns << ActiveRecord::ConnectionAdapters::Column.new(name.to_s, default, sql_type.to_s, null)
  end

end 
```
Now build the model classes you require, making sure to extend from your new base class.
```
class AnObject < LegacyObject

  column :object_id, :integer

  # and so on...

end 
```
If you just require read-only access then you're now done. If you require migration support then a bit more work is needed.

Create a new base class for the migrations that redefines the connection.
```
class LegacyMigration < ActiveRecord::Migration
  def self.connection
    LegacyDatasource.connection
  end
end 
```
Now, extend from the new base class and write your migration as normal.
```
class AMigration < LegacyMigration
  def self.up   
    # ...
  end

  def self.down
    # ...
  end
end 
```
And you now have a complete solution. Despite the lack of official documentation, it's surprisingly easy to do.
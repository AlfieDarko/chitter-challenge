require 'rake'
require 'pg'

  require 'rspec/core/rake_task'

  RSpec::Core::RakeTask.new :spec

  task :default do

    %w[chitter chitter_test].each do |database|
      p 'dropping data!'
      connection = PG.connect(dbname: database)

      begin
        connection.exec("DROP TABLE users CASCADE")
        p "dropped users"
        connection.exec("DROP TABLE peeps CASCADE")

        p "dropped peeps table from #{database}"
        rescue StandardError
          p "Couldnt remove peeps table from #{database}#{StandardError}"
      end

      begin
        connection.exec("DROP DATABASE #{database};")
        p "dropped #{database} database!"

      rescue StandardError
        p "couldnt drop #{database}"
      end
    end

    %w[chitter chitter_test].each do |database|
      begin
        connection = PG.connect
        connection.exec("CREATE DATABASE #{database};")
        rescue StandardError
          p "#{database} Database already exists"
      end

      connection = PG.connect(dbname: database)

      begin
            connection.exec(
              "CREATE TABLE users(
                id SERIAL PRIMARY KEY,
                email VARCHAR(60),
                password VARCHAR(140),
                realname VARCHAR(20),
                username VARCHAR(15),
                UNIQUE (username)
              )"
            )
                rescue StandardError
                  p "users Table already exists in #{database}"
          end

      begin
        connection.exec('CREATE TABLE peeps(
          id SERIAL PRIMARY KEY,
          author VARCHAR(15),
          text VARCHAR(140),
          time TIMESTAMP DEFAULT NOW(),
          reply_id INTEGER NULL,
          FOREIGN KEY (reply_id) REFERENCES peeps(id),
          FOREIGN KEY (author) REFERENCES users(username)
        );')
        rescue StandardError
          p "peeps Table already exists in #{database}"
      end

      p "Setting up test users"
      connection.exec("INSERT INTO users (
        email,
        password,
        realname,
        username
        )
        VALUES (
          'me@alfiedarko.co.uk',
          'alfiepw123',
          'alfie darko',
          '@alfie'
          )")

      p "setting up test peep post"

      connection.exec("INSERT INTO peeps (
        author,
        text
        ) VALUES (
          '@alfie',
          'My first tweet!'
          )"
        )

    end
  end

task :teardown do
  p 'Tearing down databases...'

  %w[chitter chitter_test].each do |database|
    # connection = PG.connect(dbname: "#{database}", user: "alfiedarko")
    connection = PG.connect(dbname: database)

    begin
      connection.exec("DROP TABLE users CASCADE")
      p "dropped users"
      connection.exec("DROP TABLE peeps CASCADE")

      p "dropped peeps table from #{database}"
      rescue StandardError
        p "Couldnt remove peeps table from #{database}#{StandardError}"
    end

    begin
      connection.exec("DROP DATABASE #{database};")
      p "dropped #{database} database!"

    rescue StandardError
      p "couldnt drop #{database}"
    end
  end

end

task :test_environment do
  connection = PG.connect(dbname: "chitter")
  connection.exec("INSERT INTO users (
    email,
    password,
    realname,
    username
    )
    VALUES (
      'me@alfiedarko.co.uk',
      'alfiepw123',
      'alfie darko',
      '@alfiedarko'
      )")
  connection.exec("INSERT INTO peeps (
    author,
    text,
    realname,
    username
    ) VALUES (
      '@alfie',
      'My first tweet!',
      )"
    )
end

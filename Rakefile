require 'bundler/setup'
require 'gdbm'
require 'sqlite3'
require 'csv'
require 'json'

task default: %w[usage]

task :usage do
  puts "Read the README!"
end

task :clean do
  sh 'rm -f shapes.sqlite'
  sh 'rm -f zip2zone.gdbm'
end

task load_shapes: %[shapes.sqlite] do
  puts "shapes.sqlite in place."
end

task zip2zone: %[zip2zone.gdbm] do
  puts "zip2zone.dbm in place."
end

file "shapes.sqlite" => %[ophz/ophz-c] do
  sh 'rm -f shapes.sqlite'
  sh 'spatialite_tool -i -shp ophz/ophz-c/ophz-c -d shapes.sqlite -t zoneshapes -c UTF8'
  puts "Updated shapes.sqlite"
end

file "ophz/ophz-c" do
  puts "Downloading OPHZ from github.  This will take a bit."
  Dir.chdir('ophz') do
    sh 'git submodule init'
    sh 'git submodule update'
  end
end

file "zip2zone.gdbm" => %w[shapes.sqlite zip2latlong.csv] do
  db = SQLite3::Database.new('shapes.sqlite')
  # how to enable extensions: https://github.com/sparklemotion/sqlite3-ruby/issues/39#issuecomment-1365999
  db.enable_load_extension(1)
  db.load_extension('mod_spatialite')
  db.enable_load_extension(0)
  gdbm = GDBM.new("zip2zone.gdbm", 0600, GDBM::NEWDB)
  count = 0
  CSV.foreach('zip2latlong.csv', headers: true) do |row|
    # puts "ST_Point(#{row[2]}, #{row[1]})"
    if db_row = db.execute("select zone, temp from zoneshapes where ST_Contains(geometry, ST_Point(?,?))", [row[2].to_f, row[1].to_f])
      db_row = db_row[0]
      if db_row
        data = JSON.dump(zone: db_row[0], temp: db_row[1])
        puts "   = " + data
        gdbm[row[0].to_s] = data
      end
    end
    count = count + 1
    if count % 100 == 0
      puts "Processed #{count} zips"
    end
  end
  
  gdbm.close
  puts "Updated zip2zone.gdbm"
end

file "zip2latlong.csv" do 
  puts "Read README.md to see how to get zip2latlong.csv in place."
end
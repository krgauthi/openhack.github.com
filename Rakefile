desc "compile and run the site"
task :default do
  pids = [
    spawn("jekyll"),
    spawn("scss --watch assets:stylesheets"),
  ]

  trap "INT" do
    Process.kill "INT", *pids
    exit 1
  end

  loop do
    sleep 1
  end
end

desc "Generate a page for your city!"
task :city do
  unless name = ENV["NAME"]
    abort "Usage: rake city NAME=YOUR_CITY_NAME"
  end

  require 'fileutils'
  require 'yaml'
  require 'geocoder'

  # 'Banana City' => 'banana_city'
  # 'Banana City, NY' => 'banana_city'
  directory = name.split(', ')[0].downcase.gsub(/\s+/, '_')
  FileUtils.mkdir_p(directory)
  File.open(File.join(directory, "index.markdown"), "w") do |file|
    file.write <<-EOF
---
layout: default
title: OpenHack - #{name}
---

## #{name}

![Photo of your meetup or city!](/#{directory}/FILL_ME_IN.png)

### Info

Put some info about when and where your meetup is here.

### Next meetups

* [First meetup](http://)
* [Second Meetup](http://)

### Past meetups

Put down how many people came, maybe some photos or other fun stuff down here!
    EOF

    results = Geocoder.search(name)
    config = YAML.load_file("_config.yml")
    cities = config["cities"]
    cities << {directory => {
      "name" => name,
      "latitude" => results.first.latitude,
      "longitude" => results.first.longitude
    }}
    config["cities"] = cities.map(&:to_a).sort.map { |city| Hash[city] }

    File.open("_config.yml", "w") do |file|
      file.write config.to_yaml
    end
  end
end

desc "Return the latitude and longitude coordinates for a specified address."
task :geocode do
  unless name = ENV["ADDRESS"]
    abort "Usage: rake city ADDRESS=ADDRESS_TO_GEOCODE"
  end

  require 'geocoder'

  result = Geocoder.search(ENV["ADDRESS"]).first
  print [result.latitude, result.longitude].join(', ')
end

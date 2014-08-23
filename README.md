reqiver
=======
require 'rubygems'
require 'active_resource'
 
ENDPOINT = "https://api.pulseway.com/v1/"
# If you host your own Pulseway Enterprise Server, use:
# ENDPOINT = "https://<server_name>/api/v1/"
USERNAME = "username"
APITOKEN = "24-13-12-C6-EC-A5-58-8E-" +
           "9C-2E-12-E9-37-84-02-44-" +
           "3C-4B-C3-E5-C7-FB-74-75-" +
           "D6-0D-6D-54-FC-7E-DB-2F-" +
           "30-18-60-E1-E3-92-11-0B-" +
           "52-66-33-46-CF-43-B8-31-" +
           "68-34-A9-45-97-AC-CE-C8-" +
           "93-47-99-49-EA-D7-2A-29"
INSTANCE_ID    = "production_website_1"
INSTANCE_NAME  = "Production Web Site"
INSTANCE_GROUP = "Web Sites"
 
class PublishResource < ActiveResource::Base
  self.site = ENDPOINT
  self.user = USERNAME
  self.password = APITOKEN
  # self.proxy = "http://127.0.0.1:8888"
  self.timeout = 30
  self.include_format_in_path = false
  self.element_name = "publish"
  self.collection_name = "publish"
end
 
begin
  publish = PublishResource.new(
    :instance_id => INSTANCE_ID,
    :name => INSTANCE_NAME,
    :group => INSTANCE_GROUP,
    :description => 'Running on ip.91.71.60.196.us-west-2.compute.internal',
    :contents => [
      {
        :name => 'Status',
        :contents => [
          {
            :icon => 'error',
            :type => 'label',
            :title => 'Could not connect to the database.',
            :subtitle => "Database Connection"
          },
          {
            :icon => 'information',
            :type => 'label',
            :title => '5 hours, 39 minutes',
            :subtitle => 'Uptime'
          }
        ]
      },
      {
        :name => 'Commands',
        :contents => [
          {
            :callback_url => 'https://admin.revoproject.com/api.php?key=d41d8cd98f00b204e9800998ecf8427e&action=reset_config',
            :type => 'webhook_command',
            :title => 'Reload Configuration',
            :subtitle => 'Reads configuration from file'
          }
        ]
      }
    ],
    :next_refresh_interval_minutes => 5,
    :notify_when_offline => 'false'
  )
  puts "Publishing..."
  if publish.save
    puts "Published with response code: " + publish.response_code.to_s
    puts "Error message: " + publish.error_message unless publish.error_message.nil?
  else
    puts "Publish failed: " + publish.errors.full_messages
  end
rescue Exception => e
  puts "Publish raised an exception."
  puts e.message
  puts e.backtrace.inspect
end

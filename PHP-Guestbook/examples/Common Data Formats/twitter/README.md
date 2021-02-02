### Getting Started with Elastic Stack for Twitter
This **Getting Started with Elastic Stack** example provides sample code to ingest, analyze & visualize **Twitter stream** around a topic of interest using the Elastic Stack, i.e. Elasticsearch, Logstash and Kibana.

##### Version
Example has been tested in following versions:
- Elasticsearch 6.0
- Logstash 6.0
- Kibana 6.0

### Installation & Setup
* Follow the [Installation & Setup Guide](https://github.com/elastic/examples/blob/master/Installation%20and%20Setup.md) to install and test the Elastic Stack (*you can skip this step if you have a working installation of the Elastic Stack*)

* Run Elasticsearch & Kibana
  ```shell
    <path_to_elasticsearch_root_dir>/bin/elasticsearch
    <path_to_kibana_root_dir>/bin/kibana
    ```

* Check that Elasticsearch and Kibana are up and running.
  - Open `localhost:9200` in web browser -- should return status code 200
  - Open `localhost:5601` in web browser -- should display Kibana UI.

  **Note:** By default, Elasticsearch runs on port 9200, and Kibana run on ports 5601. If you changed the default ports during installation, change the above calls to use appropriate ports.

### Download Example Files

Download the following files in this repo to a local directory:
- `twitter_logstash.conf` - Logstash config for ingesting data into Elasticsearch
- `twitter_template.json` - template for custom mapping of fields
- `twitter_kibana.json` - config file for creating Kibana dashboard
- `twitter_dashboard.png` - screenshot of final Kibana dashboard

Unfortunately, Github does not provide a convenient one-click option to download the entire content of a subfolder in a repo. Use sample code provided below to download the required files to a local directory:

```shell
mkdir twitter_elastic_example
cd `
wget https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/twitter/twitter_logstash.conf
wget https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/twitter/twitter_template.json
wget https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/twitter/twitter_kibana.json
```

### Run Example

##### 1. Configure example to use your Twitter API keys
* Get Twitter API keys and Access Tokens

  This example uses the Twitter API to monitor Twitter feed in real time. To use this, you will first need
  to [create a Twitter app](https://apps.twitter.com/app/new) to get your Twitter API keys and Access Tokens.

* Modify Logstash config file to use your Twitter API credentials

  Modify the `input { twitter { } }` section in the `twitter_logstash.conf` file to use the API keys and Access tokens generated   in the previous step. While at it, feel free to modify the words you want to track in the `keywords` field (in this example,    we are tracking tweets mentioning popular Marvel Comic characters.
```
input {
  twitter {
    consumer_key       => "INSERT YOUR CONSUMER KEY"
    consumer_secret    => "INSERT YOUR CONSUMER SECRET"
    oauth_token        => "INSERT YOUR ACCESS TOKEN"
    oauth_token_secret => "INSERT YOUR ACCESS TOKEN SECRET"
    keywords           => [ "thor", "spiderman", "wolverine", "ironman", "hulk"]
    full_tweet         => true
  }
}
```

##### 2. Ingest data into Elasticsearch using Logstash

* Execute the following command to start ingesting tweets of interest into Elasticsearch. Since this example is a monitoring Twitter in real time, the tweet ingestion volume will depend on the popularity of the words being tracked. When you run the above command, you should see a trail of dots (`...`) in your shell as new tweets are ingested.

  ```shell
   <path_to_logstash_root_dir>/bin/logstash -f twitter_logstash.conf
  ```


Note: Ensure this command is executed from the `twitter_elastic_example` folder.

* Verify that data is successfully indexed into Elasticsearch

  Running `http://localhost:9200/twitter_elastic_example/_count` should show a positive response for `count`.

  **Note:** Included `twitter_logstash.conf` configuration file assumes that you are running Elasticsearch on the same host as   Logstash and have not changed the defaults. Modify the `host` and `cluster` settings in the `output { elasticsearch { ... } }`   section of `twitter_logstash.conf`, if needed.


##### 3. Visualize data in Kibana

* Access Kibana by going to `http://localhost:5601` in a web browser
* Connect Kibana to the `twitter_elastic_example` index in Elasticsearch (autocreated in step 2)
    * Click the **Management** tab >> **Index Patterns** tab >> ** Add New. Specify `twitter_elastic_example` as the index pattern name and click **Create** to define the index pattern (Leave the **Use event times to create index names** box unchecked and the Event time as @timestamp)
    * If this is the only index pattern declared, you will also need to select the star in the top upper right to ensure a default is defined.
* Load sample dashboard into Kibana
    * Click the **Management** tab >> **Saved Objects** tab >> **Import**, and select `twitter_kibana.json`. On import you will be asked to overwrite existing objects - select "Yes, overwrite all". Additionally, select the index pattern "twitter_elastic_example" when asked to specify a index pattern for the dashboards.
* Open dashboard
    * Click on **Dashboard** tab and open `Sample Twitter Dashboard` dashboard. (Since we are visualizing twitter-feed in real time here, be sure to switch on the Auto-refresh option to see your dashboard update in real time)

Voila! You should see a dashboard similar to the one below. Enjoy!
![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/Common%20Data%20Formats/twitter/twitter_dashboard.jpg?raw=true)

### We would love your feedback!

If you found this example helpful and would like to see more such getting started examples for other standard formats or web APIs, we would love your feedback. If you would like to contribute examples to this repo, we'd love that too!

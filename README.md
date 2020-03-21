Step 1: Create Kafka Producers
The first step in our plan is to configure the train stations to emit some of the events that we need. The CTA has placed a sensor on each side of every train station that can be programmed to take an action whenever a train arrives at the station.

To accomplish this, you must complete the following tasks:

Complete the code in producers/models/producer.py.

Define a value schema for the arrival event in producers/models/schemas/arrival_value.json with the following attributes:

station_id
train_id
direction
line
train_status
prev_station_id
prev_direction
Complete the code in producers/models/station.py so that:
A topic is created for each station in Kafka to track the arrival events
The station emits an arrival event to Kafka whenever the Station.run() function is called.
Ensure that events emitted to Kafka are paired with the Avro key and value schemas
Define a value schema for the turnstile event in producers/models/schemas/turnstile_value.json with the following attributes
station_id
station_name
line
Complete the code in producers/models/turnstile.py so that:
A topic is created for each turnstile for each station in Kafka to track the turnstile events
The station emits a turnstile event to Kafka whenever the Turnstile.run() function is called.
Events emitted to Kafka are paired with the Avro key and value schemas
Step 2: Configure Kafka REST Proxy Producer
Our partners at the CTA have asked that we also send weather readings into Kafka from their weather hardware. Unfortunately, this hardware is old and we cannot use the Python Client Library due to hardware restrictions. Instead, we are going to use HTTP REST to send the data to Kafka from the hardware using Kafka's REST Proxy.

To accomplish this, you must complete the following tasks:

Define a value schema for the weather event in producers/models/schemas/weather_value.json with the following attributes:
temperature
status
Complete the code in producers/models/weather.py so that:
A topic is created for weather events
The weather model emits weather event to Kafka REST Proxy whenever the Weather.run() function is called.
NOTE: When sending HTTP requests to Kafka REST Proxy, be careful to include the correct Content-Type. Pay close attention to the examples in the documentation for more information.
Events emitted to REST Proxy are paired with the Avro key and value schemas
Step 3: Configure Kafka Connect
Finally, we need to extract station information from our PostgreSQL database into Kafka. We've decided to use the Kafka JDBC Source Connector.

To accomplish this, you must complete the following tasks:

Complete the code and configuration in producers/connectors.py
Please refer to the Kafka Connect JDBC Source Connector Configuration Options for documentation on the options you must complete.
You can run this file directly to test your connector, rather than running the entire simulation.
Make sure to use the Kafka Connect API, kafka-console-consumer, and kafka-topics CLI tool to check the status and output of the Connector
To delete a misconfigured connector: CURL -X DELETE localhost:8083/connectors/stations
Step 4: Configure the Faust Stream Processor
We will leverage Faust Stream Processing to transform the raw Stations table that we ingested from Kafka Connect. The raw format from the database has more data than we need, and the line color information is not conveniently configured. To remediate this, we're going to ingest data from our Kafka Connect topic, and transform the data.

To accomplish this, you must complete the following tasks:

Complete the code and configuration in consumers/faust_stream.py
Watch Out! You must run this Faust processing application with the following command:

faust -A faust_stream worker -l info

Step 5: Configure the KSQL Table
Next, we will use KSQL to aggregate turnstile data for each of our stations. Recall that when we produced turnstile data, we simply emitted an event, not a count. What would make this data more useful would be to summarize it by station so that downstream applications always have an up-to-date count.

To accomplish this, you must complete the following tasks:

Complete the queries in consumers/ksql.py.
Tips

The KSQL CLI is the best place to build your queries. Try ksql in your workspace to enter the CLI.
You can run this file on its own simply by running python ksql.py.
Made a mistake in table creation? DROP TABLE <your_table>. If the CLI asks you to terminate a running query, you can TERMINATE <query_name>.
Step 6: Create Kafka Consumers
With all of the data in Kafka, our final task is to consume the data in the web server that is going to serve the transit status pages to our commuters.

To accomplish this, you must complete the following tasks:

Complete the code in consumers/consumer.py
Complete the code in consumers/models/line.py
Complete the code in consumers/models/weather.py
Complete the code in consumers/models/station.py
Other Resources and Documentation
In addition to the course content you have already reviewed, you may find the following examples and documentation helpful in completing this project:

Confluent Python Client Documentation

Confluent Python Client Usage and Examples

REST Proxy API Reference

Kafka Connect JDBC Source Connector Configuration Options

Directory Layout
The project consists of two main directories, producers and consumers.

The following directory layout indicates with a * the files that the student is responsible for modifying. Instructions for what is required are present as comments in each file.

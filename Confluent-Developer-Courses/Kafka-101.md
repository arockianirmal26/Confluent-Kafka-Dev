# Getting started

Here I documented the practical things that I have done in the below course<br/>
[Apache Kafka® 101](https://developer.confluent.io/courses/apache-kafka/events/)

# Set up Confluent Cloud

- Head over to the Confluent Cloud signup pageand sign up for a new account
- Enter the details and Click the Start Free button
- Verify email address & create password
- In the welcome screen enter the basic details and click 'Next'

# Create your first cluster!

- In this we will create out first cluster. Clusters hold your data and enable you to process and analyze streaming data in real-time.
- In the 'Create your first cluster' window, choose 'Advanced settings'
- Choose the 'Basic'(For learning and exploring Kafka and Confluent Cloud) cluster and click 'Begin configuration'
- Choose the cloud provider and the region and click 'continue'
- In the next 'Payment' window, you can use the code provided in the course to delay entering the credit card details for 30 days
- Launch cluster

# Billing/Credits

In general you would have 400$ worth of credits to play around for 30 days. But you could also use the promo codes provided in the courses to add some more credits by navigating to Billing & payment from the settings menu in the upper right. On that screen, go to the Payment details & contacts tab to enter the promo code.

# Create Your First Kafka Topic on Confluent Cloud

Go to the Confluent landing page by clicking on the Confluent icon on the top left corner. Then navigate to the 'Environments' - 'default' and select the cluster you created under 'Live'

- Select the Topics tab on the left-hand side of the screen, then choose Create topic
- Name your topic "poems" The default number of partitions for a topic is six, which works well for today’s use case, so go ahead and select Create with defaults
- Skip the 'Data contract' window as of now
- In the next screen, which displays your topic, select the Messages tab to view the contents of the topic (which is empty at this point). Select Produce a new message (Under 'Actions' on the top right corner): This will open a UI that lets you enter a key and value for a new message (remember that a message, or an event, is a key/value pair)
- Delete the existing data in the key and value fields, and enter "1” for the key. For the value, enter a line from a poem that may sound familiar, such as, "All that is gold does not glitter." Then Click on 'Produce'
- In a similar fashion, add the next few lines of the poem
  - 2, "Not all who wander are lost"
  - 3, "The old that is strong does not wither"
  - 4, "Deep roots are not harmed by the frost"
- The four messages will be distributed amongst the six partitions of the topic

![First Topic](assets/images/1.png)

# Set Up the Confluent CLI

Go to the Confluent landing page by clicking on the Confluent icon on the top left corner. Then navigate to the 'Environments' - 'default' and select the cluster you created under 'Live'

- select **CLI and tools** from the lower left-hand corner of the screen. From here, you’ll find instructions on how to download and update the command line tools that we’ll be using.

- Paste the curl command into a terminal to install the CLI.

```bash
curl -L --http1.1 https://cnfl.io/cli | sh -s -- -b /usr/local/bin
```

In the macos I got permission issues like 'Operation not permitted/Permission denied' So I installed like below

```bash
sudo curl -L --http1.1 https://cnfl.io/cli | sh -s -- -b $HOME/.local/bin
```

The above command downloads the Confluent CLI installer script (curl) and nstalls it into a folder you can write to: ~/.local/bin. This avoids permission issues that happen with protected system folders like /usr/local/bin. The -b option tells the installer where to put the confluent command

```bash
export PATH="$HOME/.local/bin:$PATH"
```

The above adds that folder to your terminal's PATH so that your shell can find and run confluent from anywhere

```bash
source ~/.zshrc
```

Reloads your shell config so the PATH change takes effect immediately (no need to restart Terminal). The above will apply the path change only to your current session. You’ll need to make the PATH change persistent by adding it to your ~/.zshrc file properly

Open your shell config file

```bash
nano ~/.zshrc
```

Add this line at the bottom and Save and exit nano (Control + X / Press Y to confirm / Press Enter to save)

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Reload your config

```bash
source ~/.zshrc
```

- You'll receive the latest version (check it), but note that it is a good idea once in a while to update the CLI with the following

```bash
confluent --version
confluent update
```

- From a terminal, log in to Confluent Cloud with the credentials that you used to create your Confluent Cloud account. (The --save flag saves your login details locally so that you don’t have to reenter your credentials so frequently.)

```bash
confluent login --save
```

- Next, determine your Confluent environment by running

```bash
confluent environment list
```

If your account is new, you should expect to only see one environment. Observe the output from this command, particularly the ID field. Using ID value from the previous step, run:

```bash
confluent environment use {ID}
```

- Similarly, list out all of the Kafka clusters available to you using the following

```bash
confluent kafka cluster list
```

Again, observe the ID that’s output. Then set the Kafka cluster by running:

```bash
confluent kafka cluster use {ID}
```

- In order to communicate with our Kafka cluster, we need to provide an API key and secret for the CLI to use. Using the cluster ID from earlier step, run:

```bash
confluent api-key create --resource {ID}
```

This command will output an API key and secret; save these securely somewhere. To tell the CLI to use the API key, gather the cluster ID along with the API key and execute:

```bash
confluent api-key use {API Key} --resource {ID}
```

Now your CLI is set up and ready to use!

# Produce and Consume Using the Confluent CLI

- From a terminal window, list out all of the topics available to you. You should see the poems topics that we created earlier

```bash
confluent kafka topic list
```

- Consume messages from the poems topic. The --from-beginning flag tells the consumer to start from the earliest known offset on the topic, i.e., the earliest message. Leave this consumer running in the terminal window

```bash
confluent kafka topic consume --from-beginning poems
```

- From another terminal window, begin to produce more messages to the topic. Execute the produce command with the --parse-key flag to automatically read both keys and values separated by the “:” symbol

```bash
confluent kafka topic produce poems --parse-key
```

When prompted, enter the following strings as written:

```
	5:"From the ashes a fire shall awaken"
	6:"A light from the shadows shall spring"
	7:"Renewed shall be blad that was broken"
	8:"The crownless again shall be king"
```

- Observe the messages as they’re being output in the consumer terminal window. Navigate to Confluent Cloud. From the poems topic overview page, select the Messages tab and observe where the new messages have been written

# Partitioning

Partitions are useful in allowing you to break up your topic into manageable chunks that can be stored across multiple nodes in your cluster

- From the terminal window, list your available topics. You should see only the poems topic.

```bash
confluent kafka topic list
```

- Describe the topic to see more details into the topic and its configuration values. In particular, make note of the Partition Count value, which is 6

```bash
confluent kafka topic describe poems
```

- Create two more topics with 1 and 4 partitions, respectively

```bash
confluent kafka topic create --partitions 1 poems_1
confluent kafka topic create --partitions 4 poems_4
```

- Produce data to the topics using the produce command and --parse-key flag

```bash
confluent kafka topic produce poems_1 --parse-key
```

When prompted, enter the following strings as written:

```
	1:”All that is gold does not glitter”
	2:"Not all who wander are lost"
	3:"The old that is strong does not wither"
	4:"Deep roots are not harmed by the frost"
	5:"From the ashes a fire shall awaken"
	6:"A light from the shadows shall spring"
	7:"Renewed shall be blad that was broken"
	8:"The crownless again shall be king"
```

- Repeat the above step for the poems_4 topic

- From the Confluent Cloud Console, view the newly produced messages in both topics. Note that the poems_1 topic has all eight messages in its single partition while the poems_4 topic has a slightly different distribution. This should have given you a good idea of how partitions will affect the distribution of data across your topic.

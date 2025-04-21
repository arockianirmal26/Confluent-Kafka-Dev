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

Reloads your shell config so the PATH change takes effect immediately (no need to restart Terminal)

- You'll receive the latest version (check it), but note that it is a good idea once in a while to update the CLI with the following

```bash
confluent --version
confluent update
```

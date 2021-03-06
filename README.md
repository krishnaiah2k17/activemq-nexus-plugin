# ActiveMQ Nexus Plugin

This is a plugin for Nexus that publishes events from Nexus to an ActiveMQ server. It will publish messages to the
`nexus.new-artifact` topic for each new artifact.

## Development

### Setting up Nexus

This has to be done once when setting up a Nexus for development. Download nexus from [1]. Unpack it in the root of the
project.

By default the plugin won't send any messages as it expects you to configure the JMS host, but for development you can
just put this in `sonatype-work/nexus/conf/activemq-plugin.xml`:

    <?xml version="1.0" encoding="UTF-8"?>
    <activeMqConfiguration>
      <enabled>true</enabled>
      <brokerUrl>tcp://127.0.0.1:61616</brokerUrl>
    </activeMqConfiguration>

Default username and password: admin/admin123

1. Start Nexus with `nexus-*/bin/nexus start`.
1. Open <http://localhost:8081/nexus/#security-users;anonymous>
1. Give the Anonymous user the `Repo: All Repositories (Full Control)` privilege. This is just to make development easy,
so you don't have to create a settings.xml and all that hassle. Remember to press save!

[1]: http://www.sonatype.org/nexus/go

### Setting up ActiveMq

1. Download ActiveMq: http://www.apache.org/dyn/closer.cgi?path=/activemq/5.9.1/apache-activemq-5.9.1-bin.tar.gz
1. Unpack `tar zft apache-activemq-*-bin.tar.gz`
1. Start `apache-activemq-*/bin/activemq start`

The web admin interface will be available at <http://localhost:8161/admin/topics.jsp>. Username and password: admin/admin.
The log files are located at `apache-activemq-*/data/activemq.log`.

### Building

The all-in-one command:

    mvn clean install && \
     rm -rf sonatype-work/nexus/plugin-repository/activemq-nexus-plugin-* && \
     unzip -d sonatype-work/nexus/plugin-repository target/*.zip && \
     nexus-*/bin/nexus stop && \
     echo -n > sonatype-work/nexus/logs/nexus.log && \
     nexus-*/bin/nexus start

To deploy an artifact to Nexus run this:

    mvn -f test-deploy/pom.xml deploy

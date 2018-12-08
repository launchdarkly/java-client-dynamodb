LaunchDarkly SDK for Java - DynamoDB integration
================================================

[![Circle CI](https://circleci.com/gh/launchdarkly/java-dynamodb-store.svg?style=shield)](https://circleci.com/gh/launchdarkly/java-dynamodb-store)
[![Javadocs](http://javadoc.io/badge/com.launchdarkly/launchdarkly-client.svg)](http://javadoc.io/doc/com.launchdarkly/launchdarkly-client-dynamodb-store)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bhttps%3A%2F%2Fgithub.com%2Flaunchdarkly%2Fjava-dynamodb-store.svg?type=shield)](https://app.fossa.io/projects/git%2Bhttps%3A%2F%2Fgithub.com%2Flaunchdarkly%2Fjava-dynamodb-store?ref=badge_shield)

This library provides a DynamoDB-backed persistence mechanism (feature store) for the [LaunchDarkly Java SDK](https://github.com/launchdarkly/java-client), replacing the default in-memory feature store.

The minimum version of the LaunchDarkly Java SDK for use with this library is 4.6.0. It is compatible with Java 7 and above.

It uses version 1.11 of the AWS SDK for Java; a future release will use version 2.x.

For more information, see also: [Using a persistent feature store](https://docs.launchdarkly.com/v2.0/docs/using-a-persistent-feature-store).

Quick setup
-----------

This assumes that you have already installed the LaunchDarkly Java SDK.

0. Add this library to your project:

        <dependency>
          <groupId>com.launchdarkly</groupId>
          <artifactId>launchdarkly-client-dynamodb-store</artifactId>
          <version>1.0.0</version>
        </dependency>

1. If you do not already have the AWS SDK in your project, add the DynamoDB part of it. (This needs to be added separately, rather than being included in the LaunchDarkly jar, because AWS classes are exposed in the public interface.)

        <dependency>
          <groupId>com.amazonaws</groupId>
          <artifactId>com.amazonaws:aws-java-sdk-dynamodb</artifactId>
          <version>1.11.327</version>
        </dependency>

2. Import the LaunchDarkly package and the package for this library:

        import com.launchdarkly.client.*;
        import com.launchdarkly.client.dynamodb.*;

3. When configuring your SDK client, add the DynamoDB feature store:

        DynamoDBFeatureStoreBuilder store = DatabaseComponents.dynamoDBFeatureStore("my-table-name")
            .caching(FeatureStoreCaching.enabled().ttlSeconds(30));
        
        LDConfig config = new LDConfig.Builder()
            .featureStoreFactory(store)
            .build();
        
        LDClient client = new LDClient("YOUR SDK KEY", config);

The specified table must already exist in DynamoDB. It must have a partition key called "namespace" and a sort key called "key".

By default, the DynamoDB client will try to get your AWS credentials and region name from environment variables and/or local configuration files, as described in the AWS SDK documentation. There are methods in `DynamoDBFeatureStoreBuilder` for changing the configuration options. Alternatively, if you already have a fully configured DynamoDB client object, you can tell LaunchDarkly to use that:

        DynamoDBFeatureStoreBuilder store = DatabaseComponents.dynamoDBFeatureStore("my-table-name")
            .existingClient(myDynamoDBClientInstance);

Caching behavior
----------------

To reduce traffic to DynamoDB, there is an optional in-memory cache that retains the last known data for a configurable amount of time. This is on by default; to turn it off (and guarantee that the latest feature flag data will always be retrieved from DynamoDB for every flag evaluation), configure the store as follows:

        DynamoDBFeatureStoreBuilder store = DatabaseComponents.dynamoDBFeatureStore("my-table-name")
            .caching(FeatureStoreCaching.disabled());

For other ways to control the behavior of the cache, see `DynamoDBFeatureStoreBuilder.caching()`.

About LaunchDarkly
-----------

* LaunchDarkly is a continuous delivery platform that provides feature flags as a service and allows developers to iterate quickly and safely. We allow you to easily flag your features and manage them from the LaunchDarkly dashboard.  With LaunchDarkly, you can:
    * Roll out a new feature to a subset of your users (like a group of users who opt-in to a beta tester group), gathering feedback and bug reports from real-world use cases.
    * Gradually roll out a feature to an increasing percentage of users, and track the effect that the feature has on key metrics (for instance, how likely is a user to complete a purchase if they have feature A versus feature B?).
    * Turn off a feature that you realize is causing performance problems in production, without needing to re-deploy, or even restart the application with a changed configuration file.
    * Grant access to certain features based on user attributes, like payment plan (eg: users on the ‘gold’ plan get access to more features than users in the ‘silver’ plan). Disable parts of your application to facilitate maintenance, without taking everything offline.
* LaunchDarkly provides feature flag SDKs for
    * [Java](http://docs.launchdarkly.com/docs/java-sdk-reference "Java SDK")
    * [JavaScript](http://docs.launchdarkly.com/docs/js-sdk-reference "LaunchDarkly JavaScript SDK")
    * [PHP](http://docs.launchdarkly.com/docs/php-sdk-reference "LaunchDarkly PHP SDK")
    * [Python](http://docs.launchdarkly.com/docs/python-sdk-reference "LaunchDarkly Python SDK")
    * [Go](http://docs.launchdarkly.com/docs/go-sdk-reference "LaunchDarkly Go SDK")
    * [Node.JS](http://docs.launchdarkly.com/docs/node-sdk-reference "LaunchDarkly Node SDK")
    * [Electron](http://docs.launchdarkly.com/docs/electron-sdk-reference "LaunchDarkly Electron SDK")
    * [.NET](http://docs.launchdarkly.com/docs/dotnet-sdk-reference "LaunchDarkly .Net SDK")
    * [Ruby](http://docs.launchdarkly.com/docs/ruby-sdk-reference "LaunchDarkly Ruby SDK")
    * [iOS](http://docs.launchdarkly.com/docs/ios-sdk-reference "LaunchDarkly iOS SDK")
    * [Android](http://docs.launchdarkly.com/docs/android-sdk-reference "LaunchDarkly Android SDK")
* Explore LaunchDarkly
    * [launchdarkly.com](http://www.launchdarkly.com/ "LaunchDarkly Main Website") for more information
    * [docs.launchdarkly.com](http://docs.launchdarkly.com/  "LaunchDarkly Documentation") for our documentation and SDKs
    * [apidocs.launchdarkly.com](http://apidocs.launchdarkly.com/  "LaunchDarkly API Documentation") for our API documentation
    * [blog.launchdarkly.com](http://blog.launchdarkly.com/  "LaunchDarkly Blog Documentation") for the latest product updates
    * [Feature Flagging Guide](https://github.com/launchdarkly/featureflags/  "Feature Flagging Guide") for best practices and strategies

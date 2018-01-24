# Play AutoConfig

[![Travis CI](https://travis-ci.org/gmethvin/play-autoconfig.svg?branch=master)](https://travis-ci.org/gmethvin/play-autoconfig) [![Maven](https://img.shields.io/maven-central/v/io.methvin.play/autoconfig-macros_2.12.svg)](https://mvnrepository.com/artifact/io.methvin.play/autoconfig-macros)

`AutoConfig` is a utility for type-safe configuration in Play (2.6.0 and later). The library provides a convenient macro to generate `ConfigLoader` instances for arbitrary classes.

## Usage

To add to your sbt build:

```scala
libraryDependencies += "io.methvin.play" %% "autoconfig-macros" % playAutoConfigVersion
```

Replace `playAutoConfigVersion` with the latest version: [![maven central version](https://img.shields.io/maven-central/v/io.methvin.play/autoconfig-macros_2.12.svg)](https://mvnrepository.com/artifact/io.methvin.play/autoconfig-macros)

## Example

For example, suppose I want to read configuration for a hypothetical API I'm making calls to. Let's assume I need an API key, an API password, and I want to configure the request timeout. So I create a class like this:

```scala
case class FooApiConfig(
  apiKey: String,
  apiPassword: String,
  requestTimeout: Duration
)
object FooApiConfig {
  implicit val loader: ConfigLoader[FooApiConfig] = AutoConfig.loader
  def fromConfiguration(conf: Configuration) = conf.get[FooApiConfig]("api.foo")
}
```

The `fromConfiguration` method shows how you'd use the `ConfigLoader` using the `Configuration#get` method. In this case it would read a configuration object that looks like this:

```
api.foo {
  apiKey = "abcdef"
  apiPassword = "secret"
  requestTimeout = 1 minute
}
```

The `loader` macro goes through each parameter of the default constructor and looks for a `ConfigLoader` in scope for that type, then uses the loader to load the key in the object of the same name. In this case Play already provides loaders for `String` and `Duration`. If you had a custom object type nested inside, you could generate a loader in the exact same way.

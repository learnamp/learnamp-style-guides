# Rule

We should not use Rollbar as a logging platform. To do that, use `CustomLogger` instead.

# Example

```ruby
# bad
def call_api
  response = HTTParty.get('http://example.com')

  Rollbar.error('Example API problem', response: response) unless response.success?
end


# good
def call_api
  response = HTTParty.get('http://example.com')

  CustomLogger.error('Example API problem', response: response) unless response.success?
end

# if you really, really, really, have to use Rollbar, then use it like this
def call_api
  response = HTTParty.get('http://example.com')

  Rollbar.debug('Example API problem', response: response) unless response.success?
end
```

# Reason

Rollbar errors are posted to Slack [#errors](https://learn-amp.slack.com/archives/C0ET4Q10S) and when we use Rollbar to log known codepath (not an actual Exception), then we will be spamming the channel with known errors and it will be harder to spot the actual errors.

# Rule

We should not have Flipper flags being used within css.erb files directly. To do that call a method instead,
either from a helper or class.

# Example

```ruby
# bad
<% if Flipper.enabled?(:background_colour, current_user) && @company_appearance.custom_background_color? %>
/* Custom background global CSS var */
:root { --body-bg-color: <%= @company_appearance.color(:background_color) %>; }

  /* Custom background colour elements */
  body,
  .body--pretty,
    .body--dashboard,
    .dashboards .dashboards__widgets_grid .vue-grid-item,
    .line-heading span { background-color: <%= @company_appearance.color(:background_color) %>; }
<% end %>

# good
<%= @company_appearance.background_colour_css(current_user) %>

# app/models/company_appearance.rb
  
def background_colour_css(user)
  return unless Flipper.enabled?(:background_colour, user) && custom_background_color?

  css = <<-HTML
  /* Custom background global CSS var */
  :root { --body-bg-color: #{ color(:background_color) }; }

  /* Custom background colour elements */
  body,
  .body--pretty,
    .body--dashboard,
    .dashboards .dashboards__widgets_grid .vue-grid-item,
    .line-heading span { background-color: #{ color(:background_color) }; }
  HTML

  css.html_safe
end
```

# Reason

We have captured scenarios where the first approach was causing issues on Safari and the CSS block would simply not be output.

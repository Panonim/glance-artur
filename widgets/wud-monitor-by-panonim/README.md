# What's Up Docker Monitor
This widget uses WUD api. It fetches all the containers and displayes them in Glance. It checks if container needs an update and displayes it. You can also decided if you want to toggle displaying all the container or only one's that needs an update. 

To toggle showing all containers, you need to set the variable `$showAll` to `true`. You can do this by changing the line `{{ $showAll := false }}` in the code to `{{ $showAll := true }}`. Setting it to true will also make sure that images needing an update will be displayed on top. This will display all containers, regardless of whether they need an update or not.

There's also a toggle to turn on/off a message indicating that all containers are Up-To-Date `{{ $hasUpdates := false }}`.
```
        - type: custom-api
          title: What's Up Docker?
          cache: 2h
          url: http://${WUD_URL}/api/containers/watch
          method: POST
          template: |
            <ul class="list list-gap-10 collapsible-container" data-collapse-after="3">
              {{ $showAll := false }}  {{/* Set this to true to show all containers */}}
              {{ $containers := .JSON.Array "" }}
              {{ $hasUpdates := false }} {{/* Set to true to hide up-to-date message */}}
              {{ range $index, $container := $containers }}
                {{ if $container.Bool "updateAvailable" }}
                  {{ $hasUpdates = true }}
                  <li>
                    <a class="size-h4 color-highlight block text-truncate" href="https://hub.docker.com/r/{{ $container.String "image.name" }}" target="_blank">{{ $container.String "name" }}</a>
                    <ul class="list-horizontal-text">
                      <li>Status:
                        {{ if eq ( $container.String "status" ) "running" }}
                          <span class="color-positive">●</span> Running
                        {{ else }}
                          <span class="color-negative">●</span> Not Running
                        {{ end }}
                      </li>
                      <li>Watcher: {{ $container.String "watcher" }}</li>
                      <li>Update Available: <span class="color-positive">Yes</span></li>
                    </ul>
                  </li>
                {{ end }}
              {{ end }}
              {{ if not $hasUpdates }}
                <li><span class="color-positive">You're good to go!</span></li>
              {{ end }}
              {{ if $showAll }}
                {{ range $index, $container := $containers }}
                  {{ if not ( $container.Bool "updateAvailable" ) }}
                    <li>
                      <a class="size-h4 color-highlight block text-truncate" href="https://hub.docker.com/r/{{ $container.String "image.name" }}" target="_blank">{{ $container.String "name" }}</a>
                      <ul class="list-horizontal-text">
                        <li>Status:
                          {{ if eq ( $container.String "status" ) "running" }}
                            <span class="color-positive">●</span> Running
                          {{ else }}
                            <span class="color-negative">●</span> Not Running
                          {{ end }}
                        </li>
                        <li>Watcher: {{ $container.String "watcher" }}</li>
                        <li>Update Available: <span class="color-negative">No</span></li>
                      </ul>
                    </li>
                  {{ end }}
                {{ end }}
              {{ end }}
            </ul>
```
### Environment variables
`WUD_URL` - the URL of the Whats up docker server

Template: `WUD_URL=ip:port` - You can also just reaplace the code var for it to work. 

For grabbing container no matter the state I also recommend adding this to your WUD env:
```
- WUD_WATCHER_LOCAL_WATCHALL=true
```
Please remember to restart your services after applying env vars.

### Preview
[![showAll var = false](./preview1.png)](./preview1.png)

[![showAll var = true](./preview_2.png)](./preview2.png)
<hr>
Made by: Artur Flis

Contact: @blue.dev on Discord

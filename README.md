![Grafana Logo (Light)](docs/logo-horizontal.png#gh-light-mode-only)
![Grafana Logo (Dark)](docs/logo-horizontal-dark.png#gh-dark-mode-only)

The open-source platform for monitoring and observability

[![License](https://img.shields.io/github/license/grafana/grafana)](LICENSE)
[![Drone](https://drone.grafana.net/api/badges/grafana/grafana/status.svg)](https://drone.grafana.net/grafana/grafana)
[![Go Report Card](https://goreportcard.com/badge/github.com/grafana/grafana)](https://goreportcard.com/report/github.com/grafana/grafana)

Grafana allows you to query, visualize, alert on and understand your metrics no matter where they are stored. Create, explore, and share dashboards with your team and foster a data-driven culture:

- **Visualizations:** Fast and flexible client side graphs with a multitude of options. Panel plugins offer many different ways to visualize metrics and logs.
- **Dynamic Dashboards:** Create dynamic & reusable dashboards with template variables that appear as dropdowns at the top of the dashboard.
- **Explore Metrics:** Explore your data through ad-hoc queries and dynamic drilldown. Split view and compare different time ranges, queries and data sources side by side.
- **Explore Logs:** Experience the magic of switching from metrics to logs with preserved label filters. Quickly search through all your logs or streaming them live.
- **Alerting:** Visually define alert rules for your most important metrics. Grafana will continuously evaluate and send notifications to systems like Slack, PagerDuty, VictorOps, OpsGenie.
- **Mixed Data Sources:** Mix different data sources in the same graph! You can specify a data source on a per-query basis. This works for even custom datasources.

## Get started

- [Get Grafana](https://grafana.com/get)
- [Installation guides](https://grafana.com/docs/grafana/latest/setup-grafana/installation/)

Unsure if Grafana is for you? Watch Grafana in action on [play.grafana.org](https://play.grafana.org/)!

## Documentation

The Grafana documentation is available at [grafana.com/docs](https://grafana.com/docs/).

## Contributing

If you're interested in contributing to the Grafana project:

- Start by reading the [Contributing guide](https://github.com/grafana/grafana/blob/HEAD/CONTRIBUTING.md).
- Learn how to set up your local environment, in our [Developer guide](https://github.com/grafana/grafana/blob/HEAD/contribute/developer-guide.md).
- Explore our [beginner-friendly issues](https://github.com/grafana/grafana/issues?q=is%3Aopen+is%3Aissue+label%3A%22beginner+friendly%22).
- Look through our [style guide and Storybook](https://developers.grafana.com/ui/latest/index.html).

## Get involved

- Follow [@grafana on Twitter](https://twitter.com/grafana/).
- Read and subscribe to the [Grafana blog](https://grafana.com/blog/).
- If you have a specific question, check out our [discussion forums](https://community.grafana.com/).
- For general discussions, join us on the [official Slack](https://slack.grafana.com) team.

This project is tested with [BrowserStack](https://www.browserstack.com/)

## License

Grafana is distributed under [AGPL-3.0-only](LICENSE). For Apache-2.0 exceptions, see [LICENSING.md](https://github.com/grafana/grafana/blob/HEAD/LICENSING.md).

https://www.oldliew.com/post/mac-m1%E7%BC%96%E8%AF%91grafana%E6%BA%90%E7%A0%81%E5%B9%B6%E5%AE%89%E8%A3%85%E4%BD%BF%E7%94%A8/
https://www.cnblogs.com/xiaoqi/p/grafana.html
https://blog.csdn.net/lejun_wang1984/article/details/135430858


# golang proxy
```go
package main

import (
	"fmt"
	"net/http"
	"net/http/httputil"
	"net/url"
)

// GrafanaProxyHandler 代理Grafana用作管理面板
type GrafanaProxyHandler struct{}

func (h *GrafanaProxyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	// 获取当前用户
	currentUser := getCurrentUserLogin()
	if currentUser == "" {
		return
	}

	// 创建代理请求
	proxyURL, err := url.Parse("http://192.168.3.8:32204/") // 替换为您的Grafana URL
	proxy := httputil.NewSingleHostReverseProxy(proxyURL)
	fmt.Println("err", err)

	// 设置用户
	r.Header.Set("Auth", currentUser)
	fmt.Println(">>")
	// 执行代理请求
	proxy.ServeHTTP(w, r)
}

func getCurrentUserLogin() string {
	// 实现获取当前用户的逻辑
	// 例如：从会话中获取当前用户的登录信息
	return "admin" // 这里返回示例用户
}

func main() {
	handler := &GrafanaProxyHandler{}
	http.Handle("/", handler)
	fmt.Println("Server is running at :8080")
	http.ListenAndServe(":8080", nil)
}

```
- grafana config
```
grafana.ini:
  ## grafana Authentication can be enabled with the following values on grafana.ini
  server:
    root_url: "https://grafana.powerlaw.club/grafana"
    serve_from_sub_path: true
  security:
    allow_embedding: true
  # auth.anonymous:
  #   enabled: true
  #   org_name: Main Org.
  #   org_role: Viewer
  auth.proxy:
    enabled: true
    header_name: Auth
    header_property: username
    auto_sign_up: true
    sync_ttl: 60
    enable_login_token: true
```
- 隐藏侧边栏
```
# url 加 &kiosk=tv
http://127.0.0.1:8080/grafana/d/pDLS-OFSk/zhu-ji-zi-yuan-jian-kong?orgId=1&kiosk=tv
```

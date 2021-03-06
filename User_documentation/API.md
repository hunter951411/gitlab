##GitLab API
###Mục Lục
- [1. Resources](#resources)
<ul>
<li>	[1.1. Internal CI API](#internalciapi)</li>
</ul>
- [2. Authentication](#authentication)
<ul>
<li>	[2.1. Private Tokens](#privatetokens)</li>
<li>	[2.2. OAuth 2 Tokens](#oauth2tokens)</li>
<li>	[2.3. Personal Access Tokens](#personalaccesstokens)</li>
<li>	[2.4. Session Cookie](#sessioncookie)</li>
</ul>
- [3. Basic Usage](#basicusage)
- [4. Status codes](#statuscodes)
- [5. Sudo](#sudo)
- [6. Pagination](#pagination)
<ul>
<li>  [6.1. Pagination Link header](#paginationlinkheader)</li>
<li>  [6.2. Other pagination headers](#otherpaginationheaders)</li>
</ul>
- [7. id vs iid](#idvsidd)
- [8. Data validation and error reporting](#datavalidationanderrorreporting)
- [9. Unknown route](#unknownroute)
- [10. Clients](#clients)

##GitLab API

Tự động hóa GitLab qua một API đơn giản và mạnh mẽ. Tất cả các định nghĩa có thể được tìm thấy dưới  `/lib/api`.
<a name="resources"></a>
###1. Resources (Tài nguyên)
- Tài liệu cho nguồn API khác nhau có thể được tìm thấy trong các địa điểm sau:
<ul>
<li>[Award Emoji](https://docs.gitlab.com/ce/api/award_emoji.html)</li>
<li>[Branches](https://docs.gitlab.com/ce/api/branches.html)</li>
<li>[Broadcast Messages](https://docs.gitlab.com/ce/api/broadcast_messages.html)</li>
<li>[Builds](https://docs.gitlab.com/ce/api/builds.html)</li>
<li>[Build Triggers](https://docs.gitlab.com/ce/api/build_triggers.html)</li>
<li>[Build Variables](https://docs.gitlab.com/ce/api/build_variables.html)</li>
<li>[Commits](https://docs.gitlab.com/ce/api/commits.html)</li>
<li>[Deployments](https://docs.gitlab.com/ce/api/deployments.html)</li>
<li>[Deploy Keys](https://docs.gitlab.com/ce/api/deploy_keys.html)</li>
<li>[Gitignores templates](https://docs.gitlab.com/ce/api/templates/gitignores.html)</li>
<li>[GitLab CI Config templates](https://docs.gitlab.com/ce/api/templates/gitlab_ci_ymls.html)</li>
<li>[Groups](https://docs.gitlab.com/ce/api/groups.html)</li>
<li>[Group Access Requests](https://docs.gitlab.com/ce/api/access_requests.html)</li>
<li>[Group Members](https://docs.gitlab.com/ce/api/members.html)</li>
<li>[Issues](https://docs.gitlab.com/ce/api/issues.html)</li>
<li>[Issue Boards](https://docs.gitlab.com/ce/api/boards.html)</li>
<li>[Keys](https://docs.gitlab.com/ce/api/keys.html)</li>
<li>[Labels](https://docs.gitlab.com/ce/api/labels.html)</li>
<li>[Merge Requests](https://docs.gitlab.com/ce/api/merge_requests.html)</li>
<li>[Milestones](https://docs.gitlab.com/ce/api/milestones.html)</li>
<li>[Open source license templates](https://docs.gitlab.com/ce/api/templates/licenses.html)</li>
<li>[Namespaces](https://docs.gitlab.com/ce/api/namespaces.html)</li>
<li>[Notes (comments)](https://docs.gitlab.com/ce/api/notes.html)</li>
<li>[Notification settings](https://docs.gitlab.com/ce/api/notification_settings.html)</li>
<li>[Pipelines](https://docs.gitlab.com/ce/api/pipelines.html)</li>
<li>[Projects including setting Webhooks](https://docs.gitlab.com/ce/api/projects.html)</li>
<li>[Project Access Requests]()</li>
<li>[Project Members](https://docs.gitlab.com/ce/api/members.html)</li>
<li>[Project Snippets](https://docs.gitlab.com/ce/api/project_snippets.html)</li>
<li>[Repositories](https://docs.gitlab.com/ce/api/repositories.html)</li>
<li>[Repository Files](https://docs.gitlab.com/ce/api/repository_files.html)</li>
<li>[Runners](https://docs.gitlab.com/ce/api/runners.html)</li>
<li>[Services](https://docs.gitlab.com/ce/api/services.html)</li>
<li>[Session](https://docs.gitlab.com/ce/api/session.html)</li>
<li>[Settings](https://docs.gitlab.com/ce/api/settings.html)</li>
<li>[Sidekiq metrics](https://docs.gitlab.com/ce/api/sidekiq_metrics.html)</li>
<li>[System Hooks](https://docs.gitlab.com/ce/api/system_hooks.html)</li>
<li>[Tags](https://docs.gitlab.com/ce/api/tags.html)</li>
<li>[Todos](https://docs.gitlab.com/ce/api/todos.html)</li>
<li>[Users](https://docs.gitlab.com/ce/api/users.html)</li>
<li>[Validate CI configuration](https://docs.gitlab.com/ce/api/ci/lint.html)</li>
<li>[Version](https://docs.gitlab.com/ce/api/version.html)</li>
</ul>

<a name="internalciapi"></a>
####1.1. Internal CI API (CI API nội bộ)
Các tài liệu sau đây là dành cho các API CI nội bộ:
<ul>
<li>Builds</li>
<li>Runners</li>
</ul>
<a name="authentication"></a>
###2. Authentication (Xác thực)
Tất cả các yêu cầu API yêu cầu xác thực thông qua một phiên cookie hoặc token. Có ba loại thẻ có sẵn: private tokens, OAuth 2 tokens, và thẻ truy cập cá nhân. 

Nếu thông tin xác thực không hợp lệ hoặc bỏ qua, một thông báo lỗi sẽ được trả lại với mã trạng thái 401:
```
{
  "message": "401 Unauthorized"
}
```
<a name="privatetokens"></a>
####2.1 Private Tokens
Bạn cần phải vượt qua một tham số private_token qua chuỗi truy vấn hoặc tiêu đề. Nếu được thông qua như là một tiêu đề, tên tiêu đề phải được `PRIVATE-TOKEN` (chữ hoa và với một dấu gạch ngang thay vì một gạch dưới). Bạn có thể tìm thấy hoặc thiết lập lại thẻ riêng của bạn trong trang tài khoản của bạn `(/profile/account)`.
<a name="oauth2tokens"></a>
####2.2 OAuth 2 Tokens
Bạn có thể sử dụng mã thông báo OAuth 2 để xác thực với các API bằng cách đi qua nó hoặc trong các tham số access_token hoặc trong Authorization header(tiêu đề ủy quyền). 

Ví dụ về sử dụng thẻ OAuth2 trong tiêu đề:
```
curl --header "Authorization: Bearer OAUTH-TOKEN" https://gitlab.example.com/api/v3/projects
```
<a name="personalaccesstokens"></a>
####2.3 Personal Access Tokens
```
Được giới thiệu vào GitLab 8. 8.
```
Bạn có thể tạo nhiều thẻ truy cập cá nhân như bạn muốn từ hồ sơ GitLab của bạn (/profile/personal_access_tokens); có lẽ một cho mỗi ứng dụng cần truy cập vào các API GitLab. 

Một khi bạn có thẻ của bạn, vượt qua nó để các API bằng cách sử dụng các tham số private_token hoặc tiêu đề PRIVATE-TOKEN.
<a name="sessioncookie"></a>
####2.4 Session Cookie
Khi đăng nhập vào GitLab như một người dùng bình thường, một cookie _gitlab_session được thiết lập. API sẽ sử dụng cookie này để xác thực nếu nó hiện diện, nhưng bằng cách sử dụng API để tạo ra một cookie phiên làm việc mới hiện chưa được hỗ trợ.
<a name="basicusage"></a>
###3. Basic Usage (Sử dụng cơ bản)
Yêu cầu API nên được bắt đầu với `api` và phiên bản API. Các phiên bản API được định nghĩa trong lib `/api.rb`. 

Ví dụ về một yêu cầu API hợp lệ:
```
GET https://gitlab.example.com/api/v3/projects?private_token=9koXpg98eAheJpvBs5tK
```
Ví dụ về một yêu cầu API giá trị sử dụng cURL và xác thực thông qua tiêu đề:
```
curl --header "PRIVATE-TOKEN: 9koXpg98eAheJpvBs5tK" "https://gitlab.example.com/api/v3/projects"
```
Các API sử dụng JSON để tuần tự hóa dữ liệu. Bạn không cần phải chỉ định `.json` vào cuối URL API.
<a name="statuscodes"></a>
###4. Status Codes (Mã trạng thái)
Các API được thiết kế để trở lại tình trạng mã số khác nhau tùy theo bối cảnh và hành động. Bằng cách này, nếu một yêu cầu dẫn đến một lỗi, người gọi có thể để có được cái nhìn sâu sắc vào những gì đã đi sai. 

Bảng dưới đây đưa ra một cái nhìn tổng quan về cách các hàm API thường cư xử.

|<b>Request type</b>| <b>Description</b>|
|-------------------|-------------------|
|`GET`|Truy cập một hoặc nhiều nguồn và trả về kết quả như JSON.|
|`POST`|Trả về `201 Created` nếu tài nguyên được tạo thành công và trả lại tài nguyên mới được tạo ra như là JSON.|
|`GET/ PUT/ DELETE`|Trả về `200 OK` nếu tài nguyên được truy cập, sửa đổi hoặc xóa thành công. Các (sửa đổi) kết quả được trả về là JSON.|
|`DELETE`|Được thiết kế để được idempotent, có nghĩa là một yêu cầu đến một tài nguyên vẫn trả về `200 OK` ngay cả khi nó đã bị xóa trước hoặc là không có sẵn. Lý do đằng sau này, đó là người dùng không thực sự quan tâm nếu tài nguyên tồn tại trước hay không.|

Bảng dưới đây cho thấy các mã trả có thể cho yêu cầu API.

|<b>Return values</b>| <b>Description</b>|
|--------------------|-------------------|
| `200 OK`| `GET`, `PUT` và `DELETE` yêu cầu thành công, tài nguyên của chính nó được trả về dưới dạng JSON. |
| `201 Created`| Yêu cầu `POST` đã thành công và tài nguyên được trả về dạng JSON. |
| `304 Not Modified`| Chỉ ra rằng các nguồn tài nguyên chưa được sửa đổi kể từ khi yêu cầu cuối cùng. |
| `400 Bad Request`| Một thuộc tính cần thiết của yêu cầu API là mất tích, e. g. , Tiêu đề của một vấn đề không được chấp nhận. |
| `401 Unauthorized`| Người sử dụng không được chứng thực, một `user token` (mã thông báo người dùng) hợp lệ là bắt buộc. |
| `403 Forbidden`| Các yêu cầu không được cho phép, e. g. , Người sử dụng không được phép xóa một dự án. |
| `404 Not Found`| Một nguồn tài nguyên không thể được truy cập, e. g. , Một ID cho một tài nguyên không thể tìm được. |
| `405 Method Not Allowed`| Các yêu cầu không được hỗ trợ. |
| `409 Conflict`| Một nguồn mâu thuẫn đã tồn tại, e. g. , Tạo ra một dự án với tên này đã tồn tại. |
| `422 Unprocessable`| Các thực thể không thể được xử lý. |
| `500 Server Error`| Trong khi xử lý yêu cầu một cái gì đó đã đi sai server-side. |

<a name="sudo"></a>
###5. Sudo
Tất cả các yêu cầu API hỗ trợ thực hiện một cuộc gọi API như thể bạn là một người dùng khác, cung cấp thẻ riêng của bạn là từ một tài khoản quản trị viên. Bạn cần phải sử dụng các tham số `sudo` hoặc thông qua chuỗi truy vấn hoặc một tiêu đề với một ID/username của người dùng mà bạn muốn thực hiện các hoạt động như. Nếu được thông qua như là một tiêu đề, tên tiêu đề phải được `SUDO` (chữ hoa).

Nếu một `private_token` không phải từ quản trị được cung cấp, sau đó một thông báo lỗi sẽ được trả lại với mã trạng thái `403`:

`
{
  "message": "403 Forbidden - Must be admin to use sudo"
}
`

Nếu ID người dùng sudo hoặc tên người dùng không thể được tìm thấy, một thông báo lỗi sẽ được trả lại với mã trạng thái `404`:

`
{
  "message": "404 Not Found: No user id or username for: <id/username>"
}
`

Ví dụ về gọi API hợp lệ và yêu cầu sử dụng cURL với yêu cầu sudo, cung cấp một tên người dùng:

`
GET /projects?private_token=9koXpg98eAheJpvBs5tK&sudo=username
`

```
curl --header "PRIVATE-TOKEN: 9koXpg98eAheJpvBs5tK" --header "SUDO: username" "https://gitlab.example.com/api/v3/projects"
```

Ví dụ về gọi API hợp lệ và yêu cầu sử dụng cURL với yêu cầu sudo, cung cấp một ID:

```
GET /projects?private_token=9koXpg98eAheJpvBs5tK&sudo=23
```

```
curl --header "PRIVATE-TOKEN: 9koXpg98eAheJpvBs5tK" --header "SUDO: 23" "https://gitlab.example.com/api/v3/projects"
```

<a name="pagination"></a>
###6. Pagination (Số trang)
Đôi khi kết quả trả về sẽ trải dài trên nhiều trang. Khi danh sách các nguồn tài nguyên bạn có thể vượt qua các tham số sau:

| <b>Parameter</b>| <b>Description</b>|
|-----------------|-------------------|
| `page`| Số trang (mặc định: `1`)|
| `per_page`| Số lượng các mục va sách trong mỗi trang (mặc định: `20`, lớn nhất: `100`)|

Trong ví dụ dưới đây, chúng tôi liệt kê 50 `namespaces` mỗi trang.

```
curl --request PUT --header "PRIVATE-TOKEN: 9koXpg98eAheJpvBs5tK" "https://gitlab.example.com/api/v3/namespaces?per_page=50
```
<a name="paginationlinkheader"></a>
####6.1. Pagination Link header (Số trang liên kết tiêu đề)
Tiêu đề liên kết được gửi trở lại với mỗi response. Họ có `rel` đặt prev/next/first/last và có chứa URL có liên quan. Xin vui lòng sử dụng các liên kết này thay vì tạo ra các URL của riêng bạn. 

Trong cURL ví dụ dưới đây, chúng tôi giới hạn đầu ra để 3 bài mỗi trang (per_page = 3) và chúng tôi yêu cầu các trang thứ hai (trang = 2) các ý kiến của các vấn đề với ID `8` trực thuộc các project với ID `8`:

```
curl --head --header "PRIVATE-TOKEN: 9koXpg98eAheJpvBs5tK" https://gitlab.example.com/api/v3/projects/8/issues/8/notes?per_page=3&page=2
```
Response sẽ là:
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1103
Content-Type: application/json
Date: Mon, 18 Jan 2016 09:43:18 GMT
Link: <https://gitlab.example.com/api/v3/projects/8/issues/8/notes?page=1&per_page=3>; rel="prev", <https://gitlab.example.com/api/v3/projects/8/issues/8/notes?page=3&per_page=3>; rel="next", <https://gitlab.example.com/api/v3/projects/8/issues/8/notes?page=1&per_page=3>; rel="first", <https://gitlab.example.com/api/v3/projects/8/issues/8/notes?page=3&per_page=3>; rel="last"
Status: 200 OK
Vary: Origin
X-Next-Page: 3
X-Page: 2
X-Per-Page: 3
X-Prev-Page: 1
X-Request-Id: 732ad4ee-9870-4866-a199-a9db0cde3c86
X-Runtime: 0.108688
X-Total: 8
X-Total-Pages: 3
```
<a name="otherpaginationheaders"></a>
####6.2. Other pagination headers (Số trang tiêu đề khác)
Số trang bổ sung các tiêu đề cũng được gửi trở lại

|<b>Header</b>|<b>Description</b>|
|-------------|------------------|
|`X-Total`| Tổng số mục |
|`X-Total-Pages`| Tổng số trang |
|`X-Per-Page`| Số lượng các bài mỗi trang |
|`X-Page`| Chỉ số của trang htại (bắt đầu từ 1)|
|`X-Next-Page`| Chỉ số trang tiếp theo |
|`X-Prev-Page`| Chỉ số của trang trước |
<a name="idvsidd"></a>
###7. Id vs Sidd
Khi bạn làm việc với API, bạn có thể nhận thấy hai lĩnh vực tương tự như API thực thể: `id` và `iid`. Sự khác biệt chính giữa chúng là phạm vi. 

Ví dụ, một vấn đề có thể có `id: 46` và `iid: 5`.

|<b>Parameter</b>|<b>Description</b>|
|----------------|------------------|
| `id` | Là duy nhất trên tất cả các vấn đề vsử dụng cho bất kì lời gọi API |
| `iid` | Duy nhất chỉ là trong phạm vi của một dự án duy nhất. Khi bạn duyệt qua các vấn đề hoặc kết hợp các yêu cầu với giao diện Web, bạn thấy iid |

Điều đó có nghĩa rằng nếu bạn muốn nhận được một vấn đề thông qua API, bạn nên sử dụng: 
```
id`:`GET /projects/42/issues/:id
```
Mặt khác, nếu bạn muốn tạo một liên kết đến một trang web, bạn nên sử dụng `iid`:
```
GET /projects/42/issues/:iid
```
<a name="datavalidationanderrorreporting"></a>
###8. Data Validation and Error Reporting (Xác nhận dữ liệu và báo cáo lỗi)
Khi làm việc với API của bạn có thể gặp phải lỗi xác thực, trong trường hợp đó các API sẽ trả lời với trạng thái HTTP `400`.
Như vậy lỗi xuất hiện trong hai trường hợp: 
Một yêu cầu thuộc tính yêu cầu API là mất tích, ví dụ, tiêu đề của một vấn đề không được chấp nhận
Một thuộc tính đã không vượt qua xác nhận, ví dụ, người dùng đăng nhập lâu 
Khi một thuộc tính là mất tích, bạn sẽ nhận được một cái gì đó như:
```
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
    "message":"400 (Bad request) \"title\" not given"
}
```
Khi xảy ra một lỗi xác thực, thông báo lỗi sẽ khác nhau. Họ sẽ giữ tất cả các chi tiết lỗi xác thực:
```
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
    "message": {
        "bio": [
            "is too long (maximum is 255 characters)"
        ]
    }
}
```
Điều này làm cho thông báo lỗi machine-readable. Các định dạng có thể được mô tả như sau:
```
{
    "message": {
        "<property-name>": [
            "<error-message>",
            "<error-message>",
            ...
        ],
        "<embed-entity>": {
            "<property-name>": [
                "<error-message>",
                "<error-message>",
                ...
            ],
        }
    }
}
```
<a name="unknownroute"></a>
###9. Unkown Route (Không biết đường đi)
Khi bạn cố gắng truy cập một API URL không tồn tại, bạn sẽ nhận được `404 Not Found`.
```
HTTP/1.1 404 Not Found
Content-Type: application/json
{
    "error": "404 Not Found"
}
```
<a name="clients"></a>
###10. Clients
Có rất nhiều khách hàng API GitLab không chính thức cho hầu hết các ngôn ngữ lập trình phổ biến. Truy cập vào trang [Gitlab Website](https://about.gitlab.com/applications/#api-clients) cho một danh sách đầy đủ.

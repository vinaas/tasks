> Đây là các thống nhất cơ bản để phát triển một trang WebAdmin

### 2 Trang cơ bản của một 1 module 

#### 1. Trang danh sách:
- hiển thị danh sách đối tượng mà users được phép xem, filter theo groupId (từ current UserId)
- filter (search) actions theo 1 số fields của đối tượng

- Các loại hành động (quick actions) trên trang danh sách 
  - tạo mới 1 đối tượng
  - hành động sau khi lựa chọn 1 hoặc nhiều đối tượng, ví dụ **Selected Delete**, **Selected Approve**
  - từng đối tượng trong danh sách có 1 hành động mặc định là **Edit**
  
#### 2. Trang chi tiết
- hiện ra sau khi users bấm vào **Create** hoặc **Edit** 
- hiển thị dưới dạng popup trong trang và dùng để tạo mới hoặc điều chỉnh các trường thông tin 
- Một số hành động (actions) thay đổi trạng thái của 1 đối tượng `Confirm | Approve | Delete | ...`
   - có thể bổ sung trường lưu **Lý Do** cho hành động này

### 4 biến cơ bản sử dụng cho 2 trang trên

1. `listItems` chứa danh sách hiện tại, binding với giao diện
1. `filters` binding với phần Filter | Search của trang
1. `selectedItem` dùng để binding với trang Chi tiết sau khi **Create** hoặc **Edit**
1. `selectedList` dùng để binding ẩn hiện và xử lý hành động **Selected Delete**, ...

### Cách xử lý 
#### 1. Các tình huống GetAll | filter | search ở trang Danh Sách
> mặc định ban đầu  `filters = null`, `listItems = []`

> GetAll tương ứng cho trường hợp lần đầu truy cập vào trang

1. Cập nhật giá trị của filters
   - `getAll` : áp dụng giá trị mặc định của `filter = null`
   - khi User chọn 1 trường để filter, cập nhật giá trị của trường đó của filters
   - ngay cả khi search, thì cũng cập nhật giá trị vào trường `text` của filters

2. Dùng chung 1 xử lý
   - gom trong 1 function GetAll(filters), _hoặc GetObjects() , với Objects là Users, Groups, Documents, ..._
   - gọi API `/object?filter="value_of_filters"`, với token dính kèm ở Header Request. Trên server nhận biến filters, nhớ bổ sung thêm trường `userId`, `page=0` mặc định và tiến hành filters như bình thường
  
3. ghi đè vào biến `listItems`
4. Phần còn lại là binding của Aurelia

#### 2. Khi create | Edit ở trang chi tiết
> mặc định biến `selectedItem = null`

1. Cập nhật `selectedItem`
   - Trường hợp **Create**, update `selectedItem = new ItemModel()` với `status = new` 
   - Trường hợp **Edit**, khi chọn 1 item, cập nhật selectedItem = thatItem

2. call Details Popup , binding với selectedItem
   - tùy theo trạng thái mà hiển thị button text phù hợp

3. bấm "Create | Update", tùy theo trạng thái mà sẽ thực hiện tương ứng
   - `POST /objects`
   - `PUT /objects/{objId}`

#### 3. Khi lựa chọn nhiều items ở trang Chi tiết
- Khi users chọn 1 item, thực hiện 2 hành động
  - selectedItem = item_da_chon (dùng cho phần Edit)
  - selectedList.add(item_da_chon)

- Các actions tương ứng sẽ chuyển trạng thái từ "Invisiable" thành "Clickable"
  - Selected Delete, ...


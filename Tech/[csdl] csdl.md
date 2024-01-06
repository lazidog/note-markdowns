# CSDL Phân tán

bị trung không báo lỗi nó tự động xóa
tại sao cần có sở dữ liệu phân tán
trong suốt phân tán: Ta gọi lệnh sau ở hai phân mảnh khác nhau thì kết quả đều giống nhau, như vậy user sử dụng lệnh ta viết sẽ không cảm nhận được cơ sở dữ liệu phân tán hay tập trung
chuẩn hóa: giảm dữ liệu dư thừa
tính nhất quán: thỏa toàn bộ mẫu tiên database
Phân tán trong suốt
phân tán phức tạp, dễ sinh lỗi
phân mảnh do nhu cầu thực tế của bài toán
khi với một quan hệ khi ta phân tán nhiều site có hai khái niệm: nhân bản phân hoạch
nhân bản hỗ trợ truy vấn rất cao, nằm ít nhất hai site một phần tất cả các site hoàn toàn
cập nhật ở sai phân mảnh lâu
phân nằm duy nhât 1 site chức năng thất bại, hổ trợ thấp, cập nhật nhanh
Các kiểu phân mảnh: chiều dọc, ngang, hổn hợp

dữ liệu mỗi mảnh ít truy vấn nhanh, quản lý phân mảnh khó
quy tắt phân mảnh:ba quy tắt để bảo đảm cơ sở dữ liệu không thay đổi về mặt ngữ ngĩa:
Tính đầy đủ: xét góc độ quan hệ, xét quan hệ phân rã thành nhiều mảnh thì mỗi mục dữ liệu(đòng phân mảnh ngang, cột thuộc tính phân mãnh dọc) nằm ít nhất 1 phân mảnh =>
không mất thông tin
tính tái thiết xét quan hệ phân rã thành nhiều mảnh định nghĩa được 1 phép toán trên hệ thống phân tán sau cho phép toán đó sẽ tác động n phân mảnh mà lấy lại dữ liệu quan hệ góc
phân mảnh ngang phép hội phép kết tự nhiên phân mảnh dọc
tính tách biệt quan hệ phân rã thành nhiều mảnh thì mỗi mục dữ diệu trong R thì chi nằm tron 1 phân mảnh

phân rã dọc các thuộc tính khóa chính phải đc lặp lại trong mỗi phân mảnh

phân mảnh ngang nguyên thủy phân mãnh dựa trên 1 vị từ được định nghĩa trên 1 quan hê
phân mảnh ngang dẫn suất được dẫn ra từ phân mảnh khác
phân mảnh dọc nhận được từ phép chiếu quan hệ toàn cục trên mỗi nhóm tái thiết mỗi phân mảnh dọc phải chứa khóa chính của quan hệ nguyên thủy đó

Quy tắc để giúp cho các câu lệnh phân tán được “trong suốt phân tán”:

- Trong suốt : nghĩa là 1 SP khi ta cho thực thi ở 1 Server phân mảnh bất kỳ thì vẫn thực thi được ở tất cả các Server phân mảnh còn lại mà ta không cần chỉ lại đường đẫn đến SP cần truy xuất (người dùng không cảm nhận được là SP đang chạy trên Hệ thống phân tán).
- Quy tắc :
  a. Tên các cơ sở dữ liệu ở các Server Subscriber phải giống nhau
  b. Tên Link Server phải giống nhau

thiết kế cơ sở dữ liệu phân tán
tần số hoạt động cần ít nhất 1 ứng dụng
các mục tiêu thiết kế phân tán
+sự truy xuất cục bộ
dữ liệu ít truy vấn càng nhanh
dấu được dữ của ta với user khác trên cùng hệ thống
+tính sẵn sàng và khả tín quan hệ nhân bản hai tính là cao nhất phân hoạch thấp nhất
+sự phân bố tải số lượng đẩy các site tương đối đồng điều
+chi phí lưu trữ tốn kém mỗi sài cần 1 máy tính

phân mảnh thích hợp là phân mảnh có ít nhất 1 ứng dụng truy cập tới
cấp phát các phân mảnh
lượt đồ phân mảnh nhiều publiccation đẩy 1 site ánh xạ 1-1 nhanh k an toàn ít chi phí hạn chế không an toàn, lỗi chờ sửa
lượt đồ phân mảnh nhiều publiccation đẩy 2 subcrip cùng chứa dữ liệu của 1 pubsan toàn dữ liệu, có vấn đề hệ điều hành ứng dung jchuyeenr về sai 2 làm tiếp

tối ưu hóa trên truy vấn giảm thời gian thực hiện truy vấn
điều kiện xuất hiện nhiều lần trong where thì dùng phép biến đổi tương đương để cho điều kiện đó xuất hiện 1 lần
and xác suất cao sai đặt trước or ngược lại

Giao tác được xem như một dãy các giao tác đọc ghi trên csdl cùng với các bước tính toán cần thiết (begin tran, commit rollback, begin distributed tran)
có bốn tính chất tính nguyên tử tính nhất quán tính biệt lập tính bền vẫn
tất cả đều chạy hoặc k lệnh nào được thực thi
k có chậy nữa chừng
dữ liệu rác sp A đang chạy B chạy tới lấy mẫu tin xử lý nữa chừng sp A rollback
cô lập dữ liệu không thể chạy tới lấy dữ liệu đc
lưu vào csdl không thể rollback đc nnuawx
giao tác tập trung phân tán
tập trung
giao tác phẳng begin tran end tran rollback trả về toàn bộ
giao tác lồng giao tác con
giao tác phân tán chỉ có phẳng

xact_about sql loiix bỏ qua chạy tiếp
ms dtc điều phối tính đúng đắn của giao tác phân tán

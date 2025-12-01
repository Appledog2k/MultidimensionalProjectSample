# MultidimensionalProjectSample (SSAS Multidimensional)

## Tổng quan
MultidimensionalProjectSample là một dự án mẫu SQL Server Analysis Services (SSAS) dạng Multidimensional sử dụng dữ liệu mẫu AdventureWorksDW2019. Dự án minh họa chuỗi thành phần chuẩn của một mô hình đa chiều: Data Source, Data Source View (DSV), Dimensions, Cube và Partitions. Mục tiêu là giúp bạn học, demo, hoặc kiểm thử hiệu năng các khái niệm cơ bản của SSAS Multidimensional.

## Yêu cầu môi trường
- Windows và một instance SSAS kiểu Multidimensional (ví dụ: SQL Server 2019 Analysis Services).
- SQL Server Database Engine với cơ sở dữ liệu mẫu AdventureWorksDW2019 đã khôi phục.
- Visual Studio 2019/2022 kèm phần mở rộng “Analysis Services projects” (SSDT cho BI).
- SQL Server Management Studio (SSMS) để quản trị và duyệt cube (Browse) hoặc Microsoft Excel để Pivot.

## Cấu trúc dự án
Các tệp chính trong thư mục dự án:
- `MultidimensionalProjectSample.sln`: Solution Visual Studio.
- `MultidimensionalProjectSample.dwproj`: Project SSAS Multidimensional.
- `Adventure Works DW2019.ds`: Data Source trỏ tới AdventureWorksDW2019.
- `Adventure Works DW2019.dsv`: Data Source View định nghĩa lược đồ logic.
- `Customer.dim`, `Date.dim`, `Product.dim`: Các dimension ví dụ.
- `Adventure Works DW2019.cube`: Định nghĩa Cube và các measure group.
- `Adventure Works DW2019.partitions`: Cấu hình phân vùng cho cube/measure group.
- `MultidimensionalProjectSample.database`: Metadata triển khai cơ sở dữ liệu SSAS.

## Thiết lập nhanh
1. Khôi phục cơ sở dữ liệu mẫu AdventureWorksDW2019 vào SQL Server Database Engine.
	- Tài nguyên tham khảo: Microsoft Sample Databases (AdventureWorks).
2. Mở solution `MultidimensionalProjectSample.sln` bằng Visual Studio.
3. Cập nhật chuỗi kết nối Data Source:
	- Mở `Adventure Works DW2019.ds` → chỉnh Server/Database theo môi trường của bạn.
	- Kiểm tra Impersonation Info (ví dụ: “Use the service account” hoặc một tài khoản cụ thể có quyền đọc DB nguồn).
4. Xác nhận DSV: mở `Adventure Works DW2019.dsv` để đảm bảo các bảng/quan hệ hiển thị đúng.
5. Cấu hình triển khai (Deployment):
	- Chuột phải project → `Properties` → tab `Deployment`.
	- `Target Server`: tên instance SSAS (ví dụ `localhost` hoặc `localhost\SSAS`).
	- `Database`: tên cơ sở dữ liệu SSAS đích (ví dụ `MultidimensionalProjectSample`).
6. Build và Deploy:
	- `Build` → `Build` (hoặc `Rebuild`).
	- `Build` → `Deploy MultidimensionalProjectSample` để triển khai lên SSAS.
7. Process và duyệt cube:
	- Sau khi deploy, nếu chưa được xử lý tự động, hãy `Process` database/cube/dimensions.
	- Mở SSMS → kết nối tới SSAS → duyệt cube trong mục `Cubes` (Browse) hoặc kết nối bằng Excel Pivot.

## Thành phần chính
- **Data Source**: `Adventure Works DW2019.ds` (kết nối tới AdventureWorksDW2019).
- **Data Source View**: `Adventure Works DW2019.dsv` (mô hình hóa bảng, quan hệ, tên hiển thị).
- **Dimensions**: `Customer.dim`, `Date.dim`, `Product.dim` (hierarchy/attribute ví dụ).
- **Cube**: `Adventure Works DW2019.cube` (measure groups, measures, KPI, perspective nếu có).
- **Partitions**: `Adventure Works DW2019.partitions` (phân vùng theo năm/tháng nếu được định nghĩa).

## Ví dụ truy vấn MDX
Lưu ý: Tên cube mặc định trong mẫu Adventure Works thường là `Adventure Works`. Nếu tên cube khác, hãy thay bằng tên cube đã triển khai.

```mdx
-- Doanh thu Internet theo năm
SELECT
  { [Measures].[Internet Sales Amount] } ON COLUMNS,
  NON EMPTY [Date].[Calendar].[Calendar Year].Members ON ROWS
FROM [Adventure Works];

-- Doanh thu theo năm, lọc theo danh mục Sản phẩm (ví dụ: Bikes)
SELECT
  { [Measures].[Internet Sales Amount] } ON COLUMNS,
  NON EMPTY [Date].[Calendar].[Calendar Year].Members ON ROWS
FROM [Adventure Works]
WHERE ( [Product].[Category].&[Bikes] );
```

## Khắc phục sự cố
- **Không thể kết nối SSAS**: Kiểm tra dịch vụ `SQL Server Analysis Services` đã chạy, tên instance đúng, firewall cho phép.
- **Lỗi Process (khi xử lý)**: Xác nhận Data Source trỏ đúng server/DB; tài khoản Impersonation có quyền đọc; kiểm tra Credential/SSPI.
- **Deploy thất bại**: Đúng phiên bản/Compatibility Level giữa dự án và instance SSAS; đúng quyền triển khai.
- **Dữ liệu rỗng hoặc thiếu**: Kiểm tra bộ lọc trong DSV, quan hệ giữa bảng Fact/Dimension; đảm bảo DB nguồn có dữ liệu.
- **Excel không thấy measure/dimension**: Làm mới kết nối (Refresh All), chọn đúng database/cube sau khi deploy/process.

## Tùy biến và mở rộng
- Thêm/bớt dimension attributes, hierarchies, KPI, perspectives.
- Tạo partitions theo năm/tháng để tối ưu hiệu năng xử lý và truy vấn.
- Bật Proactive Caching, Aggregations nếu phù hợp kịch bản.
- Trỏ Data Source sang kho dữ liệu khác để thử nghiệm.

## Ghi chú
Kho lưu trữ này chỉ chứa định nghĩa dự án SSAS (metadata). Cơ sở dữ liệu nguồn AdventureWorksDW2019 không đi kèm và cần được cài đặt riêng trong môi trường của bạn.

## Đóng góp
Đề xuất/vấn đề (issue) và yêu cầu kéo (PR) đều được chào đón. Vui lòng mô tả rõ thay đổi và bối cảnh sử dụng.

---
Nếu bạn muốn, tôi có thể thêm hướng dẫn ảnh chụp màn hình (Properties, Deployment, Process, Browse) hoặc cung cấp script TMSL/AMO để tự động hóa deploy/process.
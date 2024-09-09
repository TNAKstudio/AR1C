## AR1C - Ứng dụng dựa trên SDXL để chỉnh sửa ảnh độ phân giải cao nhanh chóng và chất lượng
### Yêu cầu:
- Python 3.10
- Card màn hình có CUDA và VRAM tối thiểu 12GB
  
### Cài đặt:
- sao chép các tập tin vào thư mục và chạy `install.bat`
- sao chép các điểm kiểm tra SDXL vào thư mục 'models'
- khởi chạy `run.bat` và mở 127.0.0.1:7860 trong trình duyệt của bạn 

### Cách sử dụng
Ứng dụng này chủ yếu được sử dụng để chỉnh sửa ảnh chân dung với nhiều vùng cơ thể hở cần chỉnh sửa cẩn thận, chẳng hạn như chụp ảnh đồ lót, đồ bơi hoặc ảnh khiêu dâm rõ ràng. Chỉnh sửa thủ công với tần suất tách biệt đòi hỏi nhiều thời gian và trình độ cao của người chỉnh sửa. Đường ống Base SDXL Img2Img cho phép bạn có được kết quả chất lượng, nhưng nó bị giới hạn ở kích thước tối ưu của ảnh, ví dụ như 1024х1024. AR1C tạo ra kết quả hoàn hảo với các bước sau:
- tạo mặt nạ cho hình người trên ảnh (sử dụng Mask2Former với mặt nạ hợp nhất nếu có nhiều hơn một người)
- chia tách hình ảnh nguồn có mặt nạ thành các ô có kích thước 1024x1024 px (hoặc bạn có thể chọn một giá trị khác, nhưng SDXL được đào tạo trên kích thước này)
- tạo ra các ô mới với các tham số được chỉ định của thế hệ (các bước khử nhiễu và các bước thế hệ) và lời nhắc chung
- biên soạn các ô kết quả bằng cách pha trộn và dán hình ảnh mới vào nền gốc với mặt nạ đã tạo
- sau khi tạo, bạn có thể tạo lại bất kỳ phần nào của hình ảnh bằng lời nhắc tùy chỉnh và dán lại
- cũng hỗ trợ việc xử lý hàng loạt
- ứng dụng này cũng phù hợp để chỉnh sửa ảnh chân dung cận cảnh, nhưng tôi khuyên bạn nên thu nhỏ kích thước lại để có kết quả tốt nhất
- đối với bản cuối cùng, bạn có thể tải tệp PNG vào Photoshop, dưới dạng một lớp mới trên hình ảnh gốc và áp dụng mặt nạ cho một số vùng

### Giao diện
![image](https://github.com/wasidy/auto_retoucher/assets/122546017/64003051-4b47-47f2-89a1-787be22cadee)

1. Hình ảnh đầu vào. Nhấp để tải tệp nguồn. Nếu tệp quá nhỏ, nó sẽ được nâng cấp.
2. Các nút điều khiển chính. <kbd>GENERATE</kbd> Bắt đầu tạo tất cả các ô, <kbd>STOP</kbd> dừng tạo mà không có lỗi, <kbd>Reset all parameters</kbd> đặt lại tất cả thanh trượt về giá trị mặc định.
3. Dấu nhắc dương và âm cho toàn bộ hình ảnh. Hãy cẩn thận với dấu nhắc được chỉ định. Một số ô không tương ứng với dấu nhắc, đặc biệt là ở các giá trị khử nhiễu lớn.
4. Xử lý hình ảnh. Bạn có thể <kbd>ROTATE</kbd> hình ảnh của bạn, <kbd>RESIZE</kbd> cho giá trị cạnh ngắn. <kbd>COPY INPUT TO OUTPUT</kbd> cho phép bạn tạo và dán các ô tùy chỉnh mà không cần tạo toàn bộ. <kbd>RESIZE BATCH</kbd> hộp kiểm có nghĩa là thay đổi kích thước tất cả các hình ảnh hàng loạt thành kích thước đã chỉ định. <kbd>MASK BLUR</kbd> xác định giá trị cho mặt nạ làm mờ trước khi dán cuối cùng với alpha trên nền. <kbd>MASK EXPAND</kbd> là kích thước của mặt nạ mở rộng trước khi làm mờ. <kbd>FILL MASK</kbd> lấp đầy toàn bộ hình ảnh bằng mặt nạ. Ví dụ, bạn muốn tiếp tục nền hoặc hình người không được nhận dạng. <kbd>REMAKE FIGURE MASK</kbd> tái tạo mặt nạ với các tham số làm mờ và mở rộng mới.
5. Xử lý lưới. <kbd>Minimum overlap</kbd> à sự chồng chéo tối thiểu giữa các ô. Giá trị quá nhỏ làm giảm số lượng ô, nhưng có thể làm giảm chất lượng trộn ô. <kbd>Tile size</kbd> là kích thước của ô lưới. Tối ưu 1024x1024 cho SDXL, nhưng các giá trị khác được phép. <kbd>Minimum density</kbd> là giá trị để tính toán lưới. Nếu phần ảnh bị che trong ô quá nhỏ, không cần thiết phải tạo lại. Giá trị theo phần trăm. <kbd>REMESH GRID</kbd> tính toán lại lưới với các giá trị mới.
6. Cài đặt thế hệ. <kbd>Denoise strength</kbd> là cường độ khử nhiễu cho đường ống Img2Img. Đối với chỉnh sửa da, giá trị tốt là từ 5-10. Giá trị cao hơn có thể có tác dụng nhiều hơn đối với việc tái tạo, nhưng có thể gây ra sự không khớp các đường, đường nét và kết cấu trong các ô khác nhau. <kbd>Batch size</kbd> là số ô được tạo đồng thời. Tùy thuộc vào bộ nhớ card màn hình và kích thước ô của bạn. <kbd>Steps</kbd> is amount of generation steps. More steps - better quality. 50 is enough.là số bước thế hệ. Nhiều bước hơn - chất lượng tốt hơn. 50 là đủ.
7. Đầu ra hình ảnh. Sau khi tạo, bạn có thể lưu tệp PNG bằng cách nhấp vào RMB.
8. Tạo lại ô tùy chỉnh. Là nguồn để tạo lại, bạn có thể nhấp vào LMB trên bất kỳ hình ảnh đầu vào HOẶC đầu ra nào. Ô sẽ được tải trong cửa sổ.
9. <kbd>Tile masking</kbd> là giá trị cho hộp alpha. Áp dụng mặt nạ được tạo có nghĩa là kết quả tạo sẽ chỉ được áp dụng cho khu vực mà mặt nạ cơ sở được tạo.  <kbd>APPLY TO IMAGE</kbd> sẽ dán ô được tạo vào tọa độ gốc trên hình ảnh đầu ra, với cài đặt làm mờ. <kbd>Prompt</kbd> và <kbd>Negative prompt</kdb> là lời nhắc tùy chỉnh để tạo ô đơn.
10. Đã thêm khả năng tự động lưu kết quả được tạo ra và lưu đầu ra bằng cách nhấn nút. 
---


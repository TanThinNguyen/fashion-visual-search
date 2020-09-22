# fashion-visual-search
# **Tổng quan repo**
- [**data**]((https://github.com/TanThinNguyen/fashion-visual-search/tree/master/data))
  + **clothes_labels_full.csv**: file metadata chứa thông tin về đường dẫn, class, nhóm quần áo, tọa độ bounding box của các ảnh sản phẩm trong dataset gốc
  + **deepfashion_label_map.pbtxt**: file chứa thông tin mapping giữa id với tên nhóm quần áo (1: top, 2: bottom), dùng trong quá trình training detection model
  + **img_embeddings9600.csv**: file lưu embedding vector được tính toán trước của 9600 ảnh trích từ dataset gốc, dùng cho việc tính cosine similarity để đưa ra sản phẩm tương tự
  + **sample9600.csv**: file metadata chứa thông tin về đường dẫn, class, nhóm quần áo, tọa độ bounding box của 9600 ảnh sản phẩm trích từ dataset gốc
- [**visual-search-project**](https://github.com/TanThinNguyen/fashion-visual-search/tree/master/fashion-search-project)
  + **configs**: chứa file .config mô tả cấu trúc của detection model
- **models** (trong quá trình chạy run_on_colab.ipynb): là repo của Tensorflow Object Detection API dùng cho detection model
- **inference_graph** (trong quá trình chạy run_on_colab.ipynb): chứa detection model đã được train

# **Thông tin về DeepFashion Dataset**
Link dataset https://github.com/brandontrabucco/deepfashion_dataset
- Tổng quan
  + Gồm **289222 ảnh**, chiều cao được resize về **300px** với tỉ lệ không đổi
  + Các ảnh thuộc **50 class** khác nhau, chia thành **3 nhóm** chính (upper-body, lower-body, full-body)
  + Bound box thể hiện qua 2 cặp tọa độ (x1, y1), (x2, y2) là góc trái trên và phải dưới của bounding box
- Tập con gồm **9600 ảnh** trích ra từ dataset được dùng làm phạm vi tìm kiếm sản phẩm tương đồng với sản phẩm input của người dùng
- Cách trích tập con từ dataset
  + Chỉ lấy các sản phẩm thuộc nhóm **upper-body** và **lower-body**
  + Loại bỏ các class có số sản phẩm ít hơn 2000, còn lại **15 class**
  + Trích **640 ảnh** từ mỗi class trong 15 class trên

# **Cấu trúc model**
Gồm 2 model
- **Detection Model** sử dụng **Faster RCNN ResNet** pre-trained trên dataset COCO và được train lại trên DeepFashion dataset
- **Search Model** sử dụng **ResNet-based Autoencoder** tạo embedding và **Cosine Similarity** để so sánh sự tương đồng

# Cách hoạt động
- Ảnh input ban đầu sẽ được Detection Model xác định ra sản phẩm quần/áo chưa trong ảnh, vẽ bounding box và đưa phần ảnh chứa quần/áo đó qua search model
- Search Model sau đó sẽ tạo embedding và tìm trong kho ảnh các sản phẩm có embedding có khoảng cách gần với ảnh input

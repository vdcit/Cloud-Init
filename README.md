# CLoud-Init trong OpenStack Image Service 
#### Giới thiệu về Cloud init trong OpenStack

 Cloud- init là một công cụ được sử dụng để thực hiện các thiết lập ban đầu trên các Nodes Cloud, bao gồm networking, 
 SSH keys, timezone, user data injection, and more. Nó là một dịch vụ chạy trên Guest và hỗ trợ trên các bản phân phối
 của Linux, bao gồm: Fedora, Rhel, Ubuntu.
 Tích hợp hỗ trợ nó vào trong oVirt sẽ giúp tạo điều kiện cung cấp các máy ảo. Nó được sử dụng rộng rãi trong các Cloud 
 Soffware như là OpenStack (thông qua Heat) cũng như các nhà cung cấp như là Amazon.
 
###### Mô tả chi tiết
 Sử dụng Cloud- init để giúp cung cấp, đáp ứng yêu cầu của Guest. Đối với máy ảo trong Cloud, điều này thường được thực
 hiện trong quá trình tạo Image. Ở trong bài viết này nó sẽ được cài đặt trên một máy ảo hoặc Template.
 Sau khi cài đặt gói, Cloud- nit sẽ bắt đầu quá trình khời động và tìm kiếm "data sources" cái mà cung cấp cho nó các hướng dẫn
 về cấu hình.
 Các nguồn này được lưu trữ trên VM, 
 Đối với Python2.6 nguồn này được lưu trong thư mục /usr/lib/python2.6/site-packages/cloudinit/CloudConfig/
 Đối với Python2.7 nguồn này được lưu trong thư mục /usr/lib/python2.7/dist-packages/cloudinit/CloudConfig/

###### Hướng làm việc

######## Dùng dòng lệnh để chèn data sources vào trong Instances



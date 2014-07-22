# Cloud-Init trong OpenStack Image Service 
#### 1. Giới thiệu về Cloud init trong OpenStack

 Cloud- init là một công cụ được sử dụng để thực hiện các thiết lập ban đầu trên các Nodes Cloud, bao gồm networking, 
 SSH keys, timezone, user data injection, and more. Nó là một dịch vụ chạy trên Guest và hỗ trợ trên các bản phân phối
 của Linux, bao gồm: Fedora, Rhel, Ubuntu.
 Tích hợp hỗ trợ nó vào trong oVirt sẽ giúp tạo điều kiện cung cấp các máy ảo. Nó được sử dụng rộng rãi trong các Cloud 
 Soffware như là OpenStack (thông qua Heat) cũng như các nhà cung cấp như là Amazon.
 
##### Mô tả chi tiết
 Sử dụng Cloud- init để giúp cung cấp, đáp ứng yêu cầu của Guest. Đối với máy ảo trong Cloud, điều này thường được thực
 hiện trong quá trình tạo Image. Sau khi cài đặt gói, Cloud- nit sẽ bắt đầu quá trình khời động và tìm kiếm "data sources" cái mà cung cấp cho nó các hướng dẫn
 về cấu hình.
 Các nguồn này được lưu trữ trên VM.
 
 Đối với Python2.6 nguồn này được lưu trong thư mục <i>/usr/lib/python2.6/site-packages/cloudinit/CloudConfig/</i>.
 
 Đối với Python2.7 nguồn này được lưu trong thư mục <i>/usr/lib/python2.7/dist-packages/cloudinit/CloudConfig/</i>
 
 File cấu hình mặc định của Cloud- init nằm ở <i>/etc/cloud/cloud.cfg</i>
 
#### 2. Cách làm việc của Cloud- init 

 File cấu hình Cloud- init <i>/etc/cloud/cloud.cfg</i> chứa mặc định 3 modul là: Cloud_init_modules, Cloud_config_modules,
 Cloud_final_module. 
 
 ![img](http://i.imgur.com/AnhTGfu.png "img")
 
 Ở trong 3 modules này chứa Jobs mặc định của Cloud- init, ta có thể thay đổi các Jobs này, định nghĩa ra các Jobs mới
 
 ![img](http://i.imgur.com/z4ZxNIb.png "img")
 

 Ở đây mình đã định nghĩa ra Job <i>"mymodule"</i> mới trong phần Cloud_congif_modules, file này nêu ra đầu mục của các jobs con. Trong thư mục <i>/usr/lib/python2.7/dist-packages/cloudinit/CloudConfig/</i> bạn cũng phải viết thêm 1 file  <i>cc_mymodule.py</i> mới lập trình theo ngôn ngữ Python, file này chịu trách nhiệm thực hiện theo thông số đầu vào mà người dùng đưa ra có thể là cài đặt package, sửa file cấu hình, chèn passwd, ip, host.... Các đầu mục trong <i>/etc/cloud/cloud.cfg</i> sẽ được map với code python. 
 
 ![img](http://i.imgur.com/xTU0TKg.png "img")
 
 Ta sẽ tạo ra 1 file định dạng theo chuẩn của Cloud- init, file này sẽ được tạo trên máy chủ cài OpenStack hoặc chèn trực tiếp trên DashBoard. Đoạn code này chứa đầu mục Jobs mà ta muốn instance thực hiện khi nó boot, nó được coi như thông số đầu vào cho file chứa code python trong file <i>cc_mymodule.py</i>.
 
 Sau khi instance được tạo và boot lên lần đầu tiên nó sẽ đồng thời thực hiện các cấu hình mà ta đã viết trong file trên. Việc Cloud- init thực hiện được công việc này là do data trong file chứa dữ liệu đầu vào trên máy chủ OpenStack theo các đầu mục sẽ truyền vào trong file chứa code python định nghĩa công việc cần thực hiện <i>cc_mymodule.py</i> đã có sẵn trong instance khi tạo (vì instance đã được cài cloud- init sẵn).
 
 
#### 3. Thao tác làm việc với Cloud- init

 Các bước tạo Image có sẵn Cloud- init (thực hiện trên máy cài Ubuntu 1204 desktop và dùng Virtual machine để tạo VM) 
 - B1: Tạo một VM
 - B2: Install Os
 - B3: Install Cloud- init
 - B4: Chuẩn bị đoạn code python viết cho Jobs mà ta muốn thực hiện sau đó sửa file cấu hình <i>/etc/cloud/cloud.cfg</i> sao cho map tên đầu mục với code đã viết.
 - B5: Tạo ra image từ VM trên (định dạng cho image(qcow2), nén image lại cho nhỏ)
 - B6: Đẩy image vừa tạo lên máy chủ OpenStack

 **Ví dụ: ta chèn password để login vào image**
 
  Đoạn code theo định dạng của Cloud- init
 
 ![img](http://i.imgur.com/mJPQwiT.png "img")

  Các file cấu hình = python
  
 ![img](http://i.imgur.com/TcvT4IV.png "img")

  Các Jobs trong file <i>/etc/cloud/cloud.cfg</i>
 
 ![img](http://i.imgur.com/zKRhaZ9.png "img")

##### a. Dùng dòng lệnh để chèn data sources vào trong Instances (thực hiện trên máy chủ cài OpenStack)
 

Vừa tạo instance vừa chèn file uer-data

    nova boot cnuytest --image ubuntu1204 --flavor 1 --security-groups default --nic net-id=64abc0f8-5670-4e70-a3a5
    -b067c2cb6e63 --user-data testcloud-init.txt

 - Đợi instance boot xong, đăng nhập với uer: ubuntu và passwd giống như trong file testcloud-init.txt

##### b. Dùng giao diện Dashboard để tạo instance và chèn file cloud- init

 - Tạo máy ảo bằng dashboard

![img](http://i.imgur.com/MJQRDd4.png "img")

 - Nhập đoạn code theo Syntax của cloud-init

![img](http://i.imgur.com/PMlyrQE.png "img") 

 - Sau khi máy ảo được tạo xong, login bằng uer: ubuntu và pass: auyvl  ( :D )


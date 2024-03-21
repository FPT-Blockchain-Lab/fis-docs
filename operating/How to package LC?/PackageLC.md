# Đóng gói BLC

Hiện tại có 2 options cho việc đóng gói BLC:

- Option 1: Khách hàng sử dụng hạ tầng node bên FIS.
- Option 2: Khách hàng không muốn sử dụng hạ tầng node bên FIS (tự vận hành hạ tầng node).

**Đối với option 1:**

1. Node Architecture

Theo dự định ban đầu, Permission Layer và LC Application Layer sẽ được tách riêng để dễ dàng cho việc scalability và cung cấp thêm những features khác mà không
ảnh hưởng đến Permission Layer:

- Node Service: Fullnode/RPC client để sync-up data và gửi transaction request
  - Ví dụ: Ngân hàng A sẽ có Node Service A1, A2, A3; FPT Platform có Node Service F1, F2, F3, F4
- Account Service: những account/address sẽ tương tác với LC Platform smart contracts
  - Ví dụ: Doanh Nghiệp D - Account Service D1 và Ngân hàng A - Account Service A1, A2, A3 (đồng thời là Node Service)
- Doanh nghiệp/Ngân hàng hoàn toàn có thể có những Account Service mà không cần phải đăng ký Node Service
- Smart contracts ở LC Application Layer hoàn toàn không bị phụ thuộc vào Genesis contracts ở Permission Layer

-> Từ đó ta đưa ra 2 sự lựa chọn cho khách hàng:

- Tự vận hành 1 node và FIS add node đó vào hạ tầng node bên mình (Node Service).
- Sử dụng node của FIS -> cho phép họ join vào FIS node như một subOrg (Account Service).

2. LC Contracts

Sử dụng config cho LC contracts hiện tại (contracts address) đang sử dùng.

3. Permission Contracts

Sử dụng config cho Permission contracts hiện tại (contracts address) đang sử dùng.

4. Middleware

Sử dụng middleware hiện tại đang sử dụng.

**Đối với option 2:**

1. Node Architecture

Đối với việc khách hàng không muốn sử dụng hạ tầng node bên FIS:

- Thực hiên transfer setup hạ tầng cho khách hàng:
  - Set up network.
  - Set up node (deploy validators).
  - Deploy enhanced permission contract and apply for validators (Optional).
  - Blockchain Explorer (Blockscout) (Optional)
  - Bàn giao giao docs cho việc vận hành, setting networks, nodes.

Notes: Tuỳ mục đích của khách hàng có thể bỏ qua việc deploy enhanced permission contract nếu khách hàng không có nhu cầu về permission trong network.

2. LC Contracts

Tuỳ theo nhu cầu của khách hàng có muốn sử dụng, thêm hoặc sửa xoá các chức năng, flow của LC contracts hiện tại FIS có thể đưa ra 2 options:

- Nếu giữ nguyên flow và các chức năng như LC contracts hiện tại -> thực hiện deploy contract trên network của khách hàng (Xem trong document về deploy
  contract).
- Nếu khách hàng muốn thay đổi flow hoặc chức năng của LC contracts hoặc muốn sử dụng contracts khác:
  - Dev contract thực hiện thay đổi LC contracts hoặc dev contracts mới.
  - Sau khi update hoặc dev xong contracts mới deploy lên network của khách hàng.

3. Permission Contracts

Tuỳ theo nhu cầu của khách hàng có muốn sử dụng, thêm hoặc sửa xoá các chức năng, flow của Permission contracts hiện tại FIS có thể đưa ra 2 options:

- Sử dụng config cho Permission contracts hiện tại (contracts address) đang sử dùng -> Thực hiện install và apply configuration enhanced permission contract vào
  validator nodes như docs đã bàn giao.
- Đối việc thay đổi Permission contracts:
  - Thực hiện update helmchart fpt-blc-lab/goquorum-enhanced-permission.
  - Thực hiện install và apply configuration enhanced permission contract vào validator nodes như docs đã bàn giao.

4. Middleware

Đối với middleware nếu khách hàng không có sự thay đổi đối với LC contracts và Permission contracts thì có thể sử dụng version hiện tại của middleware đã bàn
giao. Nếu khách hàng có sự thay đổi đối với LC contracts và Permission contracts -> Update lại middleware theo sự thay đổi, sau đó có thể publish lại version
mới hoặc publish package mới.

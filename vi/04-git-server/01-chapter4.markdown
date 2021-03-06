# Git trên Máy Chủ #

Đến thời điểm này, bạn nên có khả năng áp dụng Git vào trong các tác vụ hàng ngày. Tuy nhiên, để cộng tác với một thành viên khác sử dụng Git, bạn sẽ cần một kho chứa Git từ xa/trung tâm. Mặc dù, về mặt kỹ thuật bạn có thể thực hiện các thao tác cơ bản (kéo/đẩy - đọc/ghi) với các thay đổi sử dụng kho chứa cá nhân riêng của từng người, nhưng làm vậy thường khiến bạn nản chí vì nó rất dễ gây khó hiểu cho bạn về việc mà người kia đang làm nếu như bạn không chú ý/cẩn thận. Hơn nữa, bạn cũng muốn những người cộng tác với bạn có thể truy cập vào kho chứa ngay cả khi máy tính của bạn không hoạt động - sử dụng một kho chứa chung có khả năng tin cậy cao thường hữu ích hơn. Chính vì thế phương pháp được khuyến cáo cho việc cộng tác với một người nào đó là sử dụng một kho chứa trung gian mà cả hai cùng có thể truy cập vào, và có thể thực hiện được các thao tác đọc ghi trên đó. Chúng ta sẽ gọi kho chứa này là một "Máy chủ Git"; nhưng bạn sẽ dễ nhận ra rằng Git sử dụng một phần tài nguyên rất nhỏ để lưu trữ kho chứa, chính vì vậy rất hiếm khi cần thiết phải sử dụng cả một máy chủ (vật lý hoặc ảo) cho nó.

Chạy một máy chủ Git rất đơn giản. Trước tiên bạn chọn giao thức giao tiếp mà bạn muốn cho máy chủ đó. Phần đầu tiên của chương này sẽ đề cập tới các giao thức hiện có cùng ưu nhược điểm của chúng. Các phần tiếp theo sẽ giải thích một số cài đặt đặc trưng sử dụng các giao thức đó và làm thế nào để máy chủ của bạn chạy được sử dụng chúng. Cuối cùng, chúng ta sẽ cùng điểm qua một số lựa chọn về hosting, nếu như bạn không ngại việc lưu trữ mã nguồn trên máy chủ của người khác và không muốn gặp phải các rắc rối về cài đặt cũng như bảo trì máy chủ riêng.

Nếu như bạn không có hứng thú với việc sử dụng máy chủ riêng, bạn có thể bỏ qua phần cuối cùng của chương này để xem một số lựa chọn cho việc cài đặt cho việc cài đặt tài khoản trên một dịch vụ cung cấp hosting và sau đó chuyển sang chương tiếp theo, nơi chúng ta sẽ thảo luận nhiều vấn đề đa dạng liên quan tới việc đọc ghi trên một môi trường quản lý phiên bản phân tán.

Một kho chứa từ xa thông thường là một _kho chứa rỗng_ - một kho chứa Git không có thư mục làm việc. Bởi vì nó chỉ được sử dụng cho mục đích cộng tác, không có lý do gì để có một snapshot đã được checkout trên máy tính; nó chỉ chứa dữ liệu Git mà thôi. Nói một cách đơn giản nhất, nó chỉ là một kho chứa rỗng - nơi để chứa nội dung dự án của bạn trong thư mục `.git` và không gì khác.

## Các Giao Thức ##

Git có thể sử dụng bốn giao thức mạng chính để truyền tải sử liệu đó là: Nội bộ, Secure Shell (SSH), Git, và HTTP. Ở đây chúng ta sẽ thảo luận xem chúng là gì và sử dụng hoặc không sử dụng chúng trong những hoàn cảnh nào.

Điều quan trọng cần chú ý là với ngoại lệ của các giao thức HTTP, tất cả các giao thức này đều yêu cầu Git được cài đặt và hoạt động trên máy chủ.

### Giao Thức Nội Bộ ###

Giao thức cơ bản nhất là _giao thức nội bộ_, trong đó kho chứa từ xa chính là một thư mục khác trên máy tính. Giao thức này thường được sử dụng nếu như tất cả thành viên trong nhóm đều có quyền truy cập vào một hệ thống chia sẻ tập tin ví dụ như một ổ đĩa mạng (NFS mount), hoặc trường hợp ít phổ biến hơn là tất cả mọi người truy cập vào cùng một máy tính. Trường hợp thứ hai không phải là một lựa chọn lý tưởng, bởi vì tất cả kho chứa của mã nguồn đều nằm trên cùng một máy tính, việc mất dữ liệu sẽ dễ xảy ra hơn cũng như nguy hiểm hơn.

Nếu như bạn có một hệ thống chia sẻ tập tin, bạn có thể clone, đọc, và ghi từ một kho chứa nội bộ. Để clone một kho chứa như thế này hoặc thêm chúng vào với vai trò là một remote cho một dự án đã có sẵn, thì bạn có thể sử dụng đường dẫn trực tiếp tới đó như là địa chỉ URL. Ví dụ để clone một kho chứa nội bộ, bạn có thể chạy lệnh sau:

	$ git clone /opt/git/project.git

Hoặc theo cách này:

	$ git clone file:///opt/git/project.git

Git hoạt động khác biệt một chút nếu bạn chỉ rõ `file://` ở đầu địa chỉ URL. Nếu bạn chỉ sử dụng đường dẫn, cộng thêm việc mã nguồn và đích đến nằm cùng trên một hệ thống tập tin (filesystem), Git sẽ cố gắng cố định (hardlink) các đối tượng mà nó cần. Nếu chung không nằm cùng trên một hệ thống tập tin, nó sẽ sao chép các đối tượng cần thiết sử dụng chức năng sao chép chuẩn của hệ thống. Nếu bạn chỉ định `file://`, Git sẽ khởi động các quá trình truyền tải dữ liệu qua mạng như thường lệ, tuy nhiên phương pháp này thường kém hiệu quả hơn. Lý do chính để chỉ định tiền tố `file://` là chỉ khi bạn muốn một bản sao "sạch" của kho chứa với các tham chiếu bên ngoài hoặc các đối tượng được lưu trữ ở ngoài - thường thì sau một lần nhập vào từ một hệ thống quản lý phiên bản khác hoặc tương tự (xem Chương 9 về các thao tác bảo trì). Chúng ta sẽ sử dụng đường dẫn thông thường ở đây vì nó luôn nhanh hơn.

Để thêm một kho chứa nội bộ vào dự án đã tồn tại, bạn có thể chạy lệnh sau:

	$ git remote add local_proj /opt/git/project.git

Sau đó, bạn có thể đọc và ghi từ remote đó như làm việc trên mạng.

#### Ưu Điểm ####

Ưu điểm của các kho chứa dạng "file-based" là rất đơn giản và tận dụng được các quyền hạn có sẵn trên hệ thống cũng như trong mạng nội bộ. Nếu như bạn đã có một hệ thống tập tin chia sẻ mà mọi thành viên đều có quyền truy cập, thì việc cài đặt một kho chứa trở nên rất dễ dàng. Bạn đặt kho chứa rỗng ở một nơi nào đó mà mọi người cùng có quyền truy cập và sau đó phân quyền đọc/ghi tương tự như với một thư mục chia sẻ thông thường. Chúng ta sẽ đề cập đến việc làm thế nào để xuất một bản sao của một kho chứa rỗng phục vụ riêng cho mục đích này ở phần sau, "Đưa Git lên máy chủ".

Đây cũng là một lựa chọn tốt cho việc nhanh chóng tiếp nhận công việc từ một kho chứa đang hoạt động của một người khác. Nếu bạn và đồng nghiệp đang làm việc trên cùng một dự án và họ muốn bạn checkout một thứ gì đó, thì chạy một câu lệnh giống như `git pull /home/john/project` thường dễ dàng hơn so với việc họ phải đẩy lên máy chủ từ xa để bạn kéo về sau đó.

#### Nhược Điểm ####

Nhược điểm của phương pháp này là các truy cập chia sẻ (shared access) nhìn chung khó cài đặt và truy cập hơn từ nhiều vị trí khác nhau hơn so với một truy cập mạng thông thường. Nếu bạn muốn đẩy dữ liệu từ máy tính xách tay khi bạn đang ở nhà thì phải bản kết nối đến ổ đĩa chia sẻ (mount remote disk), mà việc này thì thường khó khăn và chậm hơn so với một kết nối mạng thông thường.

Một điều quan trọng cần phải đề cập tới nữa đó là đây không phải là phương pháp nhanh nhất trong trường hợp sử dụng dữ liệu chia sẻ hay tương tự. Một kho chứa nội bộ chỉ thực sự nhanh nếu bạn truy cập được tới các dữ liệu đó nhanh. Kho chứa trên ổ đĩa mạng thường chậm hơn là kho chứa sử dụng giao thức SSH trên cùng một máy chủ, cho phép Git không cần thực hiện các thao tác đọc ghi lên ổ cứng thường xuyên trên mỗi hệ thống (máy khách).

### Giao Thức SSH ###

Chắc chắn giao thức phổ biến nhất cho Git là SSH. Bởi vì truy cập tới máy chủ thông qua SSH thường đã được cài đặt sẵn ở hầu hết mọi nơi - và nếu chưa thì cũng rất dễ dàng để thực hiện. SSH cũng là giao thức qua mạng mà bạn có thể dễ dàng đọc/ghi nhất. Hai giao thức còn lại (HTTP và Git) thông thường là chỉ-đọc, vì thế thậm chí các kho chứa của bạn có cung cấp giao thức này thì bạn vẫn cần sử dụng SSH cho việc ghi dữ liệu. SSH còn là một giao thức qua mạng có xác thực; và cũng vì nó được sử dụng rất rộng rãi nên về cơ bản nó dẫn dễ cài đặt cũng như sử dụng.

Để clone một kho chứa Git thông qua SSH, bạn có thể chỉ định địa chỉ ssh:// như sau:

	$ git clone ssh://user@server/project.git

Hoặc bạn có thể sử dụng cú pháp giống-scp ngắn gọn hơn cho SSH như sau:

	$ git clone user@server:project.git

Bạn cũng có thể không cần thiết phải chỉ rõ tên người dùng, Git sẽ ngầm hiểu là người dùng hiện tại của hệ thống.

#### Ưu Điểm ####

Có rất nhiều ưu điểm khi sử dụng SSH. Điều đầu tiên dễ nhận thấy nhất là bạn phải sử dụng nó nếu như bạn muốn xác minh quyền ghi vào kho chứa của bạn qua mạng. Thứ hai là SSH rất dễ dàng cài đặt - những người thông thạo về SSH rất phổ biến, rất nhiều quản trị mạng có kinh nghiệm về nó, và nhiều phiên bản của các hệ điều hành đã tích hợp sẵn SSH hoặc là có công cụ hỗ trợ. Tiếp đến là, truy cập thông qua SSH được bảo mật - tất cả dữ liệu truyền tải qua giao thức này được mã hóa và xác thực người dùng. Cuối cùng, cũng giống như giao thức Git và Nội bộ, SSH rất hiệu quả, dữ liệu được nén tới mức tối đa có thể trước khi truyền đi.

#### Nhược Điểm ####

Nhược điểm của SSH là bạn không thể cung cấp truy cập ẩn danh cho kho chứa của bạn. Người dùng phải có quyền truy cập vào máy của bạn thông qua SSH mới có thể truy cập được vào kho chứa, thậm chỉ là chỉ-đọc, điều này khiến SSH không thuận tiện cho các dự án mã nguồn mở. Nếu bạn chỉ sử dụng nó cho mạng lưới cộng tác của bạn thì SSH có thể là giao thức duy nhất bạn cần biết. Nếu bạn muốn cho phép quyền truy cập chỉ-đọc ẩn danh trên dự án của bạn, bạn sẽ phải cài đặt SSH cho các thao tác ghi của riêng bạn và một giao thức khác cho việc đọc của mọi người.

### Giao Thức Git ###

Tiếp đến là giao thức Git. Đây là một giao thức đặc biệt được cung cấp sẵn trong Git; nó lắng nghe trên một cổng dành riêng (9418) cho nó, và cung cấp các chức năng tương tự như giao thức SSH, nhưng tuyệt đối không có xác thực. Để một kho chứa có thể truy cập được thông qua giao thức Git, bạn phải tạo một tập tin `git-export-daemon-ok` - một kho chứa sẽ không thể hoạt động nếu không như không có tập tin này - nhưng khác hơn là không có bảo mật. Hoặc là kho chứa Git có thể được clone hoặc không. Điều này có nghĩa về cơ bản không có các thao tác ghi (push) trong giao thức này. Bạn có thể kích hoạt tính năng push; nhưng sẽ không có xác thực, khi bạn kích hoạt tính năng này lên, bất kỳ ai trên Internet cũng có thể push vào dự án của bạn miễn là người ta biết được đường dẫn tới dự án đó. Thực tế mà nói thì trường hợp này rất hiếm xảy ra.

#### Ưu Điểm ####

Giao thức Git hiện tại là giao thức nhanh nhất tồn tại. Nếu như bạn có một dự án công khai có tần xuất đọc ghi cao hoặc một dự án lớn mà không yêu cầu xác thực người dùng cho việc đọc, thì chắc chắn bạn sẽ cần cài đặt một Git daemon để đáp ứng cho dự án đó. Nó sử dụng cơ chế truyền tải dữ liệu giống như SSH nhưng không chịu ảnh hưởng của mã hóa và xác thực.

#### Nhược Điểm ####

Điểm bất lợi của giao thức Git là không hỗ trợ xác thực. Thông thường thì Git không được sử dụng là giao thức duy nhất để truy cập vào một dự án. Nhìn chung, bạn nên dùng chung với giao thức SSH cho một số lập trình viên có quyền ghi và sử dụng `git://` cho người dùng chỉ có quyền đọc. Nó cũng là giao thức khó cài đặt nhất. Nó phải chạy một chương trình quản lý được thiết kế riêng (daemon) - chúng ta sẽ xem ví dụ cài đặt nó trong phần "Gitosis" của chương này - nó yêu cầu phải cấu hình `xinetd` hoặc tương tự, một việc không phải lúc nào cũng dễ dàng. Nó cũng đòi hỏi giao tiếp với cổng 9418 thông qua tường lửa, cổng này thường không được cho phép do không phải là một cổng giao tiếp chuẩn. 

### Giao Thức HTTP/S ###

Cuối cùng là giao thức HTTP. Điểm nổi bật của giao thức HTTP hoặc HTTPS là sự đơn giản trong việc cài đặt. Cơ bản, tất cả những gì bạn cần phải làm là đặt kho chứa Git rỗng vào trong thư mục gốc của trang web và cài đặt một `post-update` hook (móc) riêng (xem chi tiết về Git hooks trong Chương 7). Bất kỳ ai có quyền truy cập vào máy chủ web nơi đặt kho chứa cũng có thể tạo bản sao kho chứa đó. Để cho phép đọc thông qua HTTP, bạn chạy lệnh sau: 

	$ cd /var/www/htdocs/
	$ git clone --bare /path/to/git_project gitproject.git
	$ cd gitproject.git
	$ mv hooks/post-update.sample hooks/post-update
	$ chmod a+x hooks/post-update

`post-update` hook trong Git chạy lệnh tương ứng (`git update-server-info`) để việc truy xuất cũng như sao chép thông qua HTTP được thực hiện một cách chính xác. Lệnh này được chạy khi bạn push vào kho chứa thông qua SSH; sau đó, mọi người có thể clone như sau: 

	$ git clone http://example.com/gitproject.git

Trong trường hợp cụ thể này, chúng ta đang sử dụng đường dẫn `/var/www/htdocs` phổ biến của cho Apache, nhưng bạn có thể sử dụng bất kỳ máy chủ web tĩnh nào (static web server) - chỉ cần đặt kho chứa rỗng vào trong đường dẫn đó. Dữ liệu của Git được cung cấp như là các tập tin tĩnh cơ bản (xem chi tiết hơn ở Chương 9).

Chúng ta cũng có thể thực hiện Git push thông qua HTTP, tuy rằng phương pháp này không được sử dụng một cách rộng rãi và nó yêu cầu bạn phải cài đặt WebDAV rất phức tạp. Chính vì không được sử dụng rộng rãi nên chúng ta sẽ không đề cập đến trong sách này. Nếu bạn quan tâm đến sử dụng giao thức HTTP-push, bạn có thể đọc thêm về việc cấu hình kho chứa riêng cho mục đích này tại `http://www.kernel.org/pub/software/scm/git/docs/howto/setup-git-server-over-http.txt`. Một điều thú vị về push thông qua HTTP là bạn của thể sử dụng bất kỳ máy chủ WebDAV nào, mà không cần đến các tính năng của Git; do đó bạn có thể sử dụng tính năng này nếu như máy chủ web của bạn có hỗ trợ WebDAV cho việc cập nhật dữ liệu của trang web.

#### Ưu Điểm ####

Ưu điểm của việc sử dụng giao thức HTTP là nó rất dễ cài đặt. Chỉ cần thực thi một vài câu lệnh cần thiết là bạn đã có thể cung cấp truy cập vào kho chứa Git của bạn cho tất cả người dùng. Thực hiện việc này chỉ yêu cầu vài phút. Giao thức HTTP cũng không sử dụng quá nhiều tài nguyên trên máy chủ của bạn. Vì cơ bản nó sử dụng một máy chủ HTTP tĩnh để cung cấp toàn bộ dữ liệu, một máy chủ Apache thông thường có thể phục vụ trung bình hàng ngàn tập tin trên một giây - vì thế việc quá tải gần như không xảy ra, thậm chí là với máy chủ nhỏ.

Bạn cũng có thể cho phép truy cập đến các kho chứa với quyền chỉ đọc thông qua HTTPS, có nghĩa là bạn có thể mã hóa các nội dung được truyền tải; hoặc nâng cao hơn nữa là bạn có thể yêu cầu các máy khách sử dụng một chứng thực SSL đã được xác nhận. Nhìn chung, nếu bạn theo phương pháp này, nó sẽ dễ dàng hơn việc sử dụng khóa SSH công khai; nhưng sử dụng chứng thực SSL đã được xác nhận hoặc các phương pháp xác thực khác dựa trên nền HTTP cho các thao tác chỉ đọc thông qua HTTPS cũng có thể là giải pháp tốt trong một số trường hợp cụ thể. 

Một điều thú vị nữa là HTTP là một giao thức phổ biến được sử dụng rất rộng rãi nên hầu hết các tường lửa đã được cấu hình sẵn cho phép truyền tải dữ liệu thông qua các cổng này. 

#### Nhược Điểm ####

Điểm bất cập của việc cho phép truy cập vào kho chứa thông qua HTTP là nó tương đối không hiệu quả đối với máy khác. Nhìn chung thời gian sao chép cũng như truy xuất dữ liệu thường diễn ra lâu hơn, và tình trạng quá tải cũng thường xuyên xảy ra hơn so với bất cứ giao thức mạng nào khác. Bởi vì nó không thực sự thông minh để chỉ truyền tải các dữ liệu cần thiết - không có xử lý động ở phía máy chủ trong các lần truyền tải dữ liệu này - giao thức HTTP thường được nhắc đến như là một giao thức _tồi_. Chi tiết hơn sự khác nhau về mặt hiệu quả giữa giao thức HTTP và các giao thức khác sẽ được trình bày trong Chương 9.

## Cài Đặt Git trên Máy Chủ ##

Bước đầu để cài đặt bất kỳ một máy chủ Git nào là bạn phải xuất một kho chứa có sẵn ra thành một kho chứa rỗng - kho chứa không bao gồm thư mục làm việc. Thực hiện việc này thường khá đơn giản.
Để clone kho chứa của bạn nhằm mục địch tạo một kho chứa rỗng mới, bạn chạy lệnh clone với tham số `--bare`. Để cho thuận tiện, các thư mục của kho chứa rỗng được lưu vào trong thư mục `.git`, như sau:

	$ git clone --bare my_project my_project.git
	Initialized empty Git repository in /opt/projects/my_project.git/

Thông báo đầu ra của lệnh này hơi khó hiểu một chút. Vì cơ bản lệnh `clone` tương đương với `git init` và sau đó là `git fetch`, chúng ta thấy một phần thông tin từ lệnh `git part`, phần tạo thư mục trống. Phần truyền tải dữ liệu thực thì lại không hiện thị thông báo cho bạn, mặc dù nó vẫn diễn ra. Bạn sẽ có một bản sao của thư mục chứa dữ liệu của Git trong thư mục `my_project.git`.

Nó gần tương đương với việc thực hiện lệnh sau:

	$ cp -Rf my_project/.git my_project.git

Có một vài sự khác biệt nho nhỏ trong tập tin cấu hình; nhưng với mục đích của bạn thì chúng gần như giống nhau hoàn toàn. Nó chỉ lấy thư mục Git chứ không lấy thư mục làm việc của bạn, và tạo một thư mục riêng cho nó.

### Đưa Kho Chứa Rỗng lên Máy Chủ ###

Khi bạn đã có một bản sao rỗng của kho chứa, tất cả những gì bạn cần phải làm là đưa nó lên máy chủ và cấu hình giao thức truy cập tới nó. Giả sửa bạn đã cài đặt một máy chủ mà bạn có quyền truy cập thông qua SSH có tên `git.example.com` và bạn muốn lưu trữ toàn bộ các kho chứa Git của bạn vào trong thư mục `/opt/git`. Bạn có thể cài đặt kho chứa mới bằng việc copy kho chứa rỗng của bạn lên máy chủ đó.

	$ scp -r my_project.git user@git.example.com:/opt/git

Tại thời điểm này, những người dùng khác cũng có quyền truy cập vào máy chủ này và quyền đọc thư mục `/opt/git` thông qua SSH có thể clone kho chứa của bạn bằng cách chạy lệnh sau:

	$ git clone user@git.example.com:/opt/git/my_project.git

Nếu một người dùng SSH vào máy chủ này và họ có quyền ghi vào thư mục `/opt/git/my_project.git`, thì có nghĩa là họ mặc định sẽ có quyền push. Git sẽ tự động thêm quyền ghi trên kho chứa nếu bạn chạy lệnh `git init` với tham số `--shared`.

	$ ssh user@git.example.com
	$ cd /opt/git/my_project.git
	$ git init --bare --shared

Bạn đã thấy việc tạo một kho chứa Git, tạo một phiên bản rỗng, và đưa nó lên máy chủ nơi bạn và các đồng nghiệp khác có quyền truy cập thông qua SSH dễ dàng như thế nào. Bây giờ thì bạn đã sẵn sàng để cộng tác với mọi người trên cùng một dự án.

Tôi nên nhắc lại rằng, đây là tất cả những gì bạn cần phải làm để có thể chạy một máy chủ Git có khả năng cho phép nhiều người dùng cùng truy cập - chỉ bằng cách tạo mới các tài khoản có quyền truy cập vào máy chủ thông qua SSH, và lưu kho chứa rỗng ở một nơi nào đó mà mọi người dùng đều có quyền đọc và ghi. Vậy là bạn đã sẵn sàng - không cần thêm bất cứ gì khác. 

Trong vài phần tới, bạn sẽ học được nhiều cách cài đặt khác phức tạp hơn. Bao gồm tạo mới tài khoản cho từng người dùng, thêm quyền đọc cho các kho chứa, cài đặt giao diện web, sử dụng các công cụ của Git và nhiều hơn thế nữa. Tuy nhiên, hãy nhớ là để cộng tác với một nhóm nhỏ trên một dự án riêng tư/cá nhân, tất cả những gì bạn _cần_ là một máy chủ SSH và một kho chứa rỗng.

### Các Cài Đặt Nhỏ ###

Nếu bạn muốn sử dụng Git cho công ty của bạn ở mức độ chỉ có vài lập trình viên, thì mọi việc sẽ rất đơn giản. Một trong những khía cách phức tạp nhất của việc cài đặt máy chủ Git là quản lý người dùng. Nếu bạn muốn phân quyền chỉ đọc cho một số người dùng và quyền đọc/ghi cho một số khác, thì việc truy cập cũng như phân quyền có thể hơi phức tạp một chút.

#### Truy Cập SSH ####

Nếu như bạn đã có một máy chủ mà tất cả các lập trình viên đều có quyền truy cập thông qua SSH, thì việc cài đặt kho chứa đầu tiên cơ bản là dễ nhất, vì bạn gần như không phải làm gì cả (như đã trình bày trong phần trước). Nếu bạn muốn quản lý quyền cũng như truy cập phức tạp hơn, thì bạn có thể quản lý dựa trên cách phân quyền thông thường đối với các tập tin của chính hệ điều hành mà máy chủ của bạn đang sử dụng.

Nếu bạn muốn đặt các kho chứa của bạn trên một máy chủ mà người bạn muốn phân quyền đọc/ghi lại không có tài khoản để truy cập vào máy chủ đó, thì bạn phải cài đặt truy cập SSH cho họ. Giả sử rằng bạn có một máy chủ đã được cài đặt sẵn "máy chủ SSH", và bạn cũng đang truy cập vào máy chủ đó thông qua SSH.

Có một số cách để bạn có thể phân quyền truy cập cho tất cả thành viên trong nhóm của bạn. Cách đầu tiên là cài đặt tài khoản cho tất cả các thành viên, cách này khá đơn giản nhưng lại cồng kềnh, không hiệu quả. Có thể bạn không muốn chạy lệnh `adduser` và đặt mật khẩu cho từng thành viên trong nhóm.

Cách thứ hai là tạo một người dùng 'git' trên máy chủ đó, yêu cầu mọi thành viên có quyền ghi gửi cho bạn khóa SSH công khai, và thêm khóa đó vào tập tin `~/.ssh/authorized_keys` của tài khoản 'git' mà bạn vừa tạo. Bây giờ thì mọi thành viên đều có thể truy cập vào máy chủ thông qua tài khoản 'git' đó. Việc này cũng không hề ảnh hưởng đến dữ liệu commit - tài khoản SSH mà bạn dùng để kết nối sẽ không ảnh hưởng đến các commit.

Một cách khác để thực hiện là xác thực máy chủ SSH của bạn thông qua một máy chủ LDAP hoặc một nguồn xác thực trung tâm (centralized authentication) nào đó mà bạn đã cài đặt. Miễn là mỗi thành viên có thể truy cập vào máy đó thông qua shell, bất kỳ cơ chế xác thực SSH nào mà bạn biết cũng hoạt động tốt.

## Tạo Khóa SSH Công Khai ##

Như đã được đề cập, nhiều máy chủ Git xác thực sử dụng khóa SSH công khai (SSH public key). Để cung cấp một khóa công khai, từng người dùng trong hệ thống của bạn phải tạo một khóa riêng nếu chưa có. Quá trình này tương tự nhau trên tất cả hệ điều hành.
Đầu tiên, bạn nên kiểm tra để chắc chắn rằng bạn chưa có khóa nào trước đó. Mặc định, các khóa SSH của một người dùng nào đó được lưu trong thư mục `~/.ssh`. Bạn có thể dễ dàng kiểm tra xem bạn đã có khóa nào hay chưa bằng việc vào thư mục đó và liệt kê nội dung của nó:

	$ cd ~/.ssh
	$ ls
	authorized_keys2  id_dsa       known_hosts
	config            id_dsa.pub

Bạn cần kiểm tra một cặp (2 tập tin) có tên something và something.pub, something ở đây thường là `id_dsa` hoặc `id_rsa`. Tập tin `.pub` chính là khóa công khai của bạn, và tập tin còn lại là khóa riêng tư. Nếu như bạn không có những tập tin này (hoặc bạn không có thư mục `.ssh`), bạn có thể tạo ra chúng bằng cách chạy một chương trình có tên `ssh-keygen`, được cung cấp cùng với gói SSH trên các hệ thống sử dụng Linux/Mac hay gói MSysGit trên Windows:

	$ ssh-keygen
	Generating public/private rsa key pair.
	Enter file in which to save the key (/Users/schacon/.ssh/id_rsa):
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	Your identification has been saved in /Users/schacon/.ssh/id_rsa.
	Your public key has been saved in /Users/schacon/.ssh/id_rsa.pub.
	The key fingerprint is:
	43:c5:5b:5f:b1:f1:50:43:ad:20:a6:92:6a:1f:9a:3a schacon@agadorlaptop.local

Trước tiên nó khẳng định lại nơi mà bạn muốn lưu khóa (`.ssh/id_rsa`), và sau đó nó hỏi bạn cụm mật khẩu (passphrase) hai lần, nhưng bạn có thể để trống nếu như bạn không muốn phải gõ mật khẩu khi sử dụng khóa.

Bây giờ, mỗi thành viên sau khi thực hiện xong việc này phải gửi khóa công khai của họ cho bạn hoặc người quản lý máy chủ Git (giả sử bạn đang cài đặt máy chủ SSH có yêu cầu khóa công khai - cách 2). Tất cả những gì họ phải làm là copy nội dụng của tập tin `.pub` và gửi cho bạn qua email. Các khóa công khai có định dạng như sau:

	$ cat ~/.ssh/id_rsa.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
	GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
	Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
	t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
	mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
	NrRFi9wrf+M7Q== schacon@agadorlaptop.local

Để xem hướng dẫn chi tiết hơn về việc tạo khóa SSH trên các hệ điều hành khác nhau, bạn có thể xem hướng dẫn về khóa SSH trên GitHub tại địa chỉ `http://github.com/guides/providing-your-ssh-key`.

## Cài Đặt Máy Chủ ##

Chúng ta hãy cùng xem qua hướng dẫn cấu hình SSH trên máy chủ. Trong ví dụ này, bạn sẽ sử dụng phương pháp `authorized_keys` cho việc xác thực người dùng. Chúng ta cũng sẽ giả sử rằng bạn đang chạy một phát hành chuẩn của Linux như Ubuntu. Trước tiên, bạn tạo một tài khoản người dùng 'git' và một thư mục `.ssh` cho người dùng đó.

	$ sudo adduser git
	$ su git
	$ cd
	$ mkdir .ssh

Tiếp theo, bạn cần phải thêm một số khóa công khai của các lập trình viên vào tập tin `authorized_keys` cho người dùng đó. Giả sử rằng bạn đã nhận được một số khóa bằng email và lưu nó vào một tập tin tạm thời nào đó. Nhắc lại là khóa công khai có định dạng tương tự như sau:

	$ cat /tmp/id_rsa.john.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCB007n/ww+ouN4gSLKssMxXnBOvf9LGt4L
	ojG6rs6hPB09j9R/T17/x4lhJA0F3FR1rP6kYBRsWj2aThGw6HXLm9/5zytK6Ztg3RPKK+4k
	Yjh6541NYsnEAZuXz0jTTyAUfrtU3Z5E003C4oxOj6H0rfIF1kKI9MAQLMdpGW1GYEIgS9Ez
	Sdfd8AcCIicTDWbqLAcU4UpkaX8KyGlLwsNuuGztobF8m72ALC/nLF6JLtPofwFBlgc+myiv
	O7TCUSBdLQlgMVOFq1I2uPWQOkOWQAHukEOmfjy2jctxSDBQ220ymjaNsHT4kgtZg2AYYgPq
	dAv8JggJICUvax2T9va5 gsg-keypair

Bạn chỉ cần thêm nó vào tập tin `authorized_keys` của bạn:

	$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
	$ cat /tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys
	$ cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys

Bây giờ, bạn có thể cài đặt một kho chứa rỗng cho họ bằng việc chạy lệnh `git init` với tham số `--bare`, lệnh này sẽ khởi tạo một kho chứa mà không có thư mục làm việc:

	$ cd /opt/git
	$ mkdir project.git
	$ cd project.git
	$ git --bare init

Sau đó, John, Josie, hay Jessica có thể push phiên bản đầu tiên của dự án của họ vào kho chứa đó bằng cách thêm nó vào như là một remote và push lên một nhánh. Lưu ý rằng, người dùng nào đó phải truy cập (shell) vào máy chủ và tạo một kho chứa rỗng mỗi khi bạn muốn thêm một dự án mới. Hãy sử dụng `gitserver` làm tên cho máy chủ của chúng ta nơi mà bạn vừa cấu hình tài khoản 'git' và kho chứa. Nếu như bạn đang chạy nội bộ và bạn càu đặt DNS cho `gitsever` trỏ tới máy chủ đó, thì bạn có thể sử dụng các câu lệnh tương tự như sau:

	# on Johns computer
	$ cd myproject
	$ git init
	$ git add .
	$ git commit -m 'initial commit'
	$ git remote add origin git@gitserver:/opt/git/project.git
	$ git push origin master

Đến thời điểm này, các thành viên khác có thể clone nó xuống và push các thay đổi ngược lên một cách dễ dàng:

	$ git clone git@gitserver:/opt/git/project.git
	$ cd project
	$ vim README
	$ git commit -am 'fix for the README file'
	$ git push origin master

Với phương pháp này, bạn có thể nhanh chóng có được một máy chủ Git được cấu hình đọc/ghi hoàn thiện cho một nhóm nhỏ lập trình viên.

Bạn cũng nên lưu ý rằng bạn có thể dễ dàng hạn chế người dùng 'git' chỉ được phép thực hiện các thao tác có liên quan đến Git bằng cách sử dụng một công cụ đã được giới hạn chức năng có tên là `git-shell` được cung cấp sẵn trong Git. Nếu như bạn cấu hình nó trở thành shell mặc định khi đăng nhập cho người dùng 'git' thì người dùng 'git' này sẽ không thể truy cập vào máy của bạn như shell thông thường. Để sử dụng tính năng này, bạn cần chỉ định `git-shell` thay vì bash hoặc csh làm shell mặc định cho người dùng đó. Bạn cần phải sửa lại tập tin `/etc/passwd`:

	$ sudo vim /etc/passwd

Ở phía cuối cùng, bạn sẽ thấy một dòng tương tự như sau:

	git:x:1000:1000::/home/git:/bin/sh

Hãy sửa `/bin/sh` thành `/usr/bin/git-shell` (hoặc chạy `which git-shell` để xem nó được cài đặt ở đâu). Sau khi sửa, nó sẽ giống như sau:

	git:x:1000:1000::/home/git:/usr/bin/git-shell

Bây giờ tài khoàn người dùng 'git' chỉ có thể sử dụng kết nối SSH để đọc và ghi dữ liệu vào các kho chứa mà không thể truy cập (shell) vào máy của bạn nữa. Nếu bạn thử, bạn sẽ nhận được một thông báo từ chối như sau:

	$ ssh git@gitserver
	fatal: What do you think I am? A shell?
	Connection to gitserver closed.

## Truy Cập Công Khai ##

Nếu như bạn muốn truy cập ẩn danh để đọc dự án của bạn thì sẽ như thế nào? Có thể thay vì việc tự lưu trữ một dự án cá nhân, bạn lại muốn lưu trữ một dự án mã nguồn mở. Hoặc cũng có thể bạn có một loạt máy chủ tự biên dịch hoặc tích hợp liên tục có rất nhiều thay đổi xảy ra, và bạn không muốn phải tạo khóa SSH mỗi lần truy cập - bạn đơn giản chỉ muốn đọc nó mà không phải qua bước xác thực người dùng.

Chắc chắn cách đơn giản nhất cho các dự án nhỏ là chạy một máy chủ web tĩnh mà thư mục gốc trùng với thư mục các kho chứa, và sau đó kích hoạt "móc" `post-update` mà chúng ta đã đề cập đến ở phần đầu của chương này. Hãy cùng sử dụng lại ví dụ trước. Giả sử bạn lưu các kho chứa trong thư mục `/opt/git`, và có một máy chủ Apache đang chạy. Tôi xin nhắc lại một lần nữa rằng bạn có thể sử dụng bất kỳ máy chủ web nào cho việc này; nhưng trong ví dụ này, một số cấu hình Apache cơ bản có thể gợi ý cho bạn cái mà bạn cần:

Trước tiên bạn cần kích hoạt `pót-update` hook:

	$ cd project.git
	$ mv hooks/post-update.sample hooks/post-update
	$ chmod a+x hooks/post-update

Vậy `post-update` hook này làm gì? Cơ bản nó giống như thực hiện các lệnh sau:

	$ cat .git/hooks/post-update
	#!/bin/sh
	exec git-update-server-info

Điều này có nghĩa là khi bạn push lên máy chủ thông qua SSH, Git sẽ chạy các lệnh này để cập nhật các tập tin cần thiết phục vụ cho việc truy xuất thông qua HTTP.

Tiếp theo, bạn cần phải thêm một bản ghi VirtualHost vào trong cấu hình của Apache chỉ định thư mục gốc của web cũng là thư mục của dự án Git. Ở đây chúng ta giả sử rằng bạn cài đặt DNS để trỏ `*.gitserver` đến một máy bất kỳ nào đó nơi bạn đang chạy tất cả những thứ này (máy này :D):

	<VirtualHost *:80>
	    ServerName git.gitserver
	    DocumentRoot /opt/git
	    <Directory /opt/git/>
	        Order allow, deny
	        allow from all
	    </Directory>
	</VirtualHost>

Bạn cũng cần phải cài đặt nhóm người dùng Unix cho thư mục `/opt/git` cho `www-data` để cho máy chủ web của bạn có thể đọc được các kho chứa, bởi vì mặc định Apache chạy các mã CGI bằng tài khoản đó: 

	$ chgrp -R www-data /opt/git

Sau khi khởi động lại Apache, bạn có thể clone các kho chứa trong thư mục đó bằng cách chỉ định đường dẫn tới dự án bạn cần:

	$ git clone http://git.gitserver/project.git

Theo các này, bạn có thể cho phép truy cập chỉ đọc dựa trên HTTP cho bất kỳ dự án nào cho với một lượng người dùng tương đối chỉ trong vài phút. Một lựa chọn đơn giản khác cho việc truy cập không cần xác thực là kích hoạt một Git daemon, mặc dù cách này đòi hỏi bạn phải ngầm hóa quá trình chạy - chúng ta sẽ đề cập về lựa chọn này trong phần tới, nếu như bạn ưa thích phương pháp đó hơn.

## GitWeb ##

Bây giờ bạn đã có thể truy cập được vào dự án của bạn thông qua hai hình thức là chỉ-đọc và đọc/ghi, có thể bạn muốn cài đặt thêm một công cụ trực quan hóa dựa trên nền web. Git cung cấp sẵn một đoạn mã CGI được thiết kế riêng cho mục đích này có tên GitWeb. Bạn có thể xem GitWeb hoạt động ra sao tại trang web `http://git.kernel.org` (xem Hình 4-1).

Insert 18333fig0401.png
Hình 4-1. Giao diện người dùng của GitWeb.

Nếu bạn muốn tìm hiểu xem GitWeb hoạt động như thế nào trên dự án của bạn, Git cung cấp một câu lệnh để khởi động một máy chủ web đơn giản tạm thời trên hệ thống của bạn như `lighttpd` hoặc `webrick`. Trên các máy chạy Linux, `lighttpd` thường đã được cài đặt sẵn, vì thế bạn có thể có khả năng khởi động nó lên bằng cách chạy lệnh `git instaweb` từ thư mục dự án của bạn. Nếu bạn đang sử dụng Mac, Leopard đã được cài đặt sẵn Ruby, do vậy `webrick` có thể là lựa chọn tốt nhất. Để chạy `instaweb` với non-lighttpd handler, bạn có thể chạy nó với tham số `--httpd`:

	$ git instaweb --httpd=webrick
	[2009-02-21 10:02:21] INFO  WEBrick 1.3.1
	[2009-02-21 10:02:21] INFO  ruby 1.8.6 (2008-03-03) [universal-darwin9.0]

Lệnh này sẽ khởi động một máy chủ HTTPD trên cổng 1234 và sau đó tự khởi động trình duyệt và truy cập vào trang đó. Phần việc của bạn khá đơn giản. Sau khi xem xong, để tắt máy chủ đó đi bạn chạy lệnh tương tự với tham số `--stop`:

	$ git instaweb --httpd=webrick --stop

Nếu bạn muốn chạy máy chủ web liên tục cho các thành viên trong nhóm hoặc cho một dự án mã nguồn mở bạn đang lưu trữ, bạn cần phải cài đặt sao cho đoạn mã CGI đó được chạy bởi một máy chủ web thông thường. Một số phiên bản của Linux có gói `gitweb` cho bạn có thể cài đặt thông qua `apt` hoặc `yum`, do vậy hãy thử cách đó trước. Bây giờ chúng ta sẽ cùng cài đặt nhanh chóng GitWeb theo cách thủ công. Trước hết, bạn cần có mã nguồn của Git, nơi có chứa GitWeb, và tạo một đoạn mã CGI:

	$ git clone git://git.kernel.org/pub/scm/git/git.git
	$ cd git/
	$ make GITWEB_PROJECTROOT="/opt/git" \
	        prefix=/usr gitweb
	$ sudo cp -Rf gitweb /var/www/

Chú ý rằng bạn phải nói cho câu lệnh trên biết phải tìm các kho chứa Git của bạn ở đâu bằng cách sử dụng biến `GITWEB_PROJECTROOT`. Bây giờ bạn cần làm cho Apache sử dụng CGI cho đoạn mã đó, sau đó chúng ta mới có thể thêm VirtualHost:

	<VirtualHost *:80>
	    ServerName gitserver
	    DocumentRoot /var/www/gitweb
	    <Directory /var/www/gitweb>
	        Options ExecCGI +FollowSymLinks +SymLinksIfOwnerMatch
	        AllowOverride All
	        order allow,deny
	        Allow from all
	        AddHandler cgi-script cgi
	        DirectoryIndex gitweb.cgi
	    </Directory>
	</VirtualHost>

Cần nhắc lại là GitWeb có thể chạy được với bất kỳ máy chủ web nào có khả năng chạy mã CGI; nếu bạn muốn dùng một loại máy chủ khác, thì cũng không quá khó để cài đặt. Bây giờ bạn đã có thể truy cập vào `http://gitserver/` để xem các kho chứa của bạn, và sử dụng `http://gitserver/` để clone và fetch khác kho chứa thông qua HTTP.

## Gitosis ##

Lưu trữ tất cả khóa công khai của người dùng trong tập tin `authorized_keys` phục vụ cho mục đích truy cập chỉ hiệu quả trong một thời gian ngắn. Khi bạn có tới hàng trăm người dùng, việc quản lý sẽ trở nên rất khó khăn. Bạn phải truy cập vào máy chủ mỗi lần, cũng như không thể quản lý được quyền truy cập - mọi người dùng có trong tập tin đó đều có quyền đọc và ghi trên mọi dự án.

Cách giải quyết cho bạn lúc này là chuyển sang dùng một phần mềm quản lý phổ biến có ten là Gitosis. Gitosis cơ bản là một tập hợp các đoạn mã giúp bạn quản lý tập tin `authorized_keys` cũng như cung cấp một số tính năng phân quyền truy cập đơn giản. Điều thú vị ở đây là giao diện phần thêm mới người dùng cũng như phân quyền truy cập của phần mềm này không phải là giao diện web mà nó lại là một kho chứa Git đặc biệt. Bạn cấu hình các thông tin cần thiết ở trong dự án đó; sau đó push nó lên, Gitosis sẽ tự cấu hình lại máy chủ dựa trên những thông tin đó. Thật tuyệt phải không nào.

Cài đặt Gitosis không phải là thao tác đơn giản nhất, nhưng cũng không hề quá khó. Cách đơn giản nhất là sử dụng một máy chủ Linux cho nó - các ví dụ sau sử dụng một máy chủ chạy Ubuntu 8.10 nguyên bản.

Gitosis yêu cầu một số công cụ được viết bằng Python, do vậy việc đầu tiên bạn phải cài đặt gói Python setuptools, được cung cấp trên Ubuntu với tên python-setuptools:

	$ apt-get install python-setuptools

Tiếp theo, bạn clone và cài đặt Gitosis từ trang web chính thức của dự án:

	$ git clone git://eagain.net/gitosis.git
	$ cd gitosis
	$ sudo python setup.py install

Lệnh này sẽ cài đặt một số chương trình mà Gitosis cần sử dụng tới. Tiếp theo, Gitosis đòi hỏi phải đặt kho chứa của nó vào thư mục `/home/git`, điều này hoàn toàn chấp nhận được. Nhưng bạn đã cài đặt các kho chứa của bạn ở thư mục `/opt/git` từ trước, do vậy thay vì cấu hình lại mọi thứ, bạn chỉ cần tạo một symlink:

	$ ln -s /opt/git /home/git/repositories

Gitosis sẽ quản lý các khóa cho bạn, vì thế bạn cần phải xóa tập tin hiện tại đi, thêm lại các khóa sau đó, và để Gitosis tự động quản lý tập tin `authorized_keys`. Bây giờ hãy di chuyển tập tin `authorized_keys` sang một nơi khác:

	$ mv /home/git/.ssh/authorized_keys /home/git/.ssh/ak.bak

Tiếp theo bạn cẩn phải kích hoạt lại shell cho tài khoản 'git', nếu như bạn đổi nó thành `git-shell` trước đây. Các thành viên vẫn sẽ không thể truy cập vào máy chủ, Gitosis sẽ quản lý việc này cho bạn. Thế nên hãy sửa dòng sau trong tập tin `/etc/passwd`:

	git:x:1000:1000::/home/git:/usr/bin/git-shell

thành như sau:

	git:x:1000:1000::/home/git:/bin/sh

Bây giờ đến lúc khởi tạo Gitosis. Bạn thực hiện việc này bằng cách chạy lệnh `gitosis-init` với khóa công khai riêng của bạn. Nếu nó không tồn tại trên máy chủ, bạn sẽ phải copy nó lên:

	$ sudo -H -u git gitosis-init < /tmp/id_dsa.pub
	Initialized empty Git repository in /opt/git/gitosis-admin.git/
	Reinitialized existing Git repository in /opt/git/gitosis-admin.git/

Lệnh này sẽ cho phép người dùng tương ứng với khóa đó chỉnh sửa kho chứa cấu hình của Gitosis. Tiếp theo bạn phải tự thêm quyền thực thi cho mã `post-update` của kho chứa mới.

	$ sudo chmod 755 /opt/git/gitosis-admin.git/hooks/post-update

Bạn đã sẵn sàng để chạy nó rồi đó. Nếu như bạn cài đặt một cách chính xác thì bạn có thể thử SSH vào máy chủ sử dụng tài khoản mà bạn đã thêm khóa công khai trong quá trình khởi tạo Gitosis. Bạn sẽ nhận được thông báo như sau:

	$ ssh git@gitserver
	PTY allocation request failed on channel 0
	fatal: unrecognized command 'gitosis-serve schacon@quaternion'
	  Connection to gitserver closed.

Điều này có nghĩa là Gitosis nhận ra bạn nhưng sau đó lại đóng kết nối của bạn lại vì bạn không chạy một câu lệnh Git nào cả. Vậy thì hãy thực thi một lệnh nào đó thôi - hãy clone chính kho chứa Gitosis:

	# on your local computer
	$ git clone git@gitserver:gitosis-admin.git

Bây giờ bạn có một thư mục có tên là `gitosis-admin`, bao gồm hai phần cơ bản:

	$ cd gitosis-admin
	$ find .
	./gitosis.conf
	./keydir
	./keydir/scott.pub

Tập tin `gitosis.conf` là tập tin mà bạn sẽ dùng để quản lý người dùng, kho chứa, và các quyền. Thư mục `keydir` là nơi dùng để lưu trữ các khóa công khai của tất cả người dùng có bất kỳ ít nhất một quyền truy cập nào đó vào các kho chứa của bạn - một tập tin cho một người dùng. Tên của tập tin trong thư mục `keydir` (trong ví dụ trước là `scott.pub`) sẽ khác cho với của bạn - Gitosis sử dụng tên trong phần mô tả ở cuối khóa công khai được nhập vào qua đoạn mã `gitosis-init`.

Nếu bạn mở tập tin `gitosis.conf` ra, bạn sẽ thấy nó chỉ chứa thông tin về dự án `gitosis-admin` mà bạn vừa clone:

	$ cat gitosis.conf
	[gitosis]

	[group gitosis-admin]
	writable = gitosis-admin
	members = scott

 Nó chỉ ra rằng tài khoản 'scott' - người dụng có khóa công khai được sử dụng khi khởi tạo - là người duy nhất có quyền truy cập vào dự án `gitosis-admin`:

Bây giờ hãy tạo một dự án của riêng bạn. Ví dụ bạn sẽ thêm một phần mới có tên `mobile` nơi bạn sẽ liệt kê các lập trình viên trong nhóm mobile và các dự án mà các lập trình đó cần truy cập vào. Bởi vì 'scott' là người dùng duy nhất của hệ thống tại thời điểm hiện tại, nên bạn sẽ thêm tài khoản đó như là thành viên duy nhất, và sau đó tạo một dự án mới có tên `iphone_project` để bắt đầu:

	[group mobile]
	writable = iphone_project
	members = scott

Bất cứ khi nào bạn thay đổi một nội dung nào đó trong dự án `gitosis-admin`, bạn phải commit các thay đổi đó và sau đó push nó ngược trở lên máy chủ thì chúng mới có hiệu lực:

	$ git commit -am 'add iphone_project and mobile group'
	[master]: created 8962da8: "changed name"
	 1 files changed, 4 insertions(+), 0 deletions(-)
	$ git push
	Counting objects: 5, done.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (3/3), 272 bytes, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To git@gitserver:/opt/git/gitosis-admin.git
	   fb27aec..8962da8  master -> master

Bạn có thể tạo push đầu tiên cho dự án mới `iphone_project` bằng cách thêm máy chủ này vào phiên bản nội bộ của bạn với vai trò là một remote. Bạn sẽ không cần phải tạo kho chứa rỗng một cách thủ công cho các dự án mới trên máy chủ nữa - Gitosis sẽ tự động tạo khi nó nhận được push đầu tiên:

	$ git remote add origin git@gitserver:iphone_project.git
	$ git push origin master
	Initialized empty Git repository in /opt/git/iphone_project.git/
	Counting objects: 3, done.
	Writing objects: 100% (3/3), 230 bytes, done.
	Total 3 (delta 0), reused 0 (delta 0)
	To git@gitserver:iphone_project.git
	 * [new branch]      master -> master

Chú ý là bạn không cần chỉ định đường dẫn (thực tế, làm như vậy sẽ khiến nó không hoạt động), mà chỉ dùng dấu hai chấm và sau đó là tên của dự án - Gitosis sẽ tự tìm đường dẫn cho bạn.

Nếu bạn muốn cộng tác với các đồng nghiệp khác trên dự án này, bạn sẽ phải thêm khóa công khai của họ lại một lần nữa. Nhưng thay vì việc thêm thủ công vào tập tin `~/.ssh/authorized_keys` trên máy chủ, thì bạn sẽ thêm vào thư mục `keydir`, mỗi tập tin chứa một khóa. Cách đặt tên kháo phụ thuộc vào cách bạn gọi đến chúng trong tập tin `gitosis.conf`. Chúng ta sẽ thêm lại khóa công khai cho các thành viên John, Josie, và Jessica:

	$ cp /tmp/id_rsa.john.pub keydir/john.pub
	$ cp /tmp/id_rsa.josie.pub keydir/josie.pub
	$ cp /tmp/id_rsa.jessica.pub keydir/jessica.pub

Bây giờ thì bạn đã có thể thêm tất cả những thành viên này vào nhóm 'mobile', điều này cũng có nghĩa là họ có quyền đọc-ghi trên dự án `iphone_project`:

	[group mobile]
	writable = iphone_project
	members = scott john josie jessica

Sau khi bạn commit và push thay đổi đó, cả bốn thành viên sẽ có khả năng đọc và ghi trên dự án này.

Gitosis quản lý truy cập khá đơn giản. Nếu bạn chỉ muốn John có quyền đọc dự án này thì bạn có thể làm như sau:

	[group mobile]
	writable = iphone_project
	members = scott josie jessica

	[group mobile_ro]
	readonly = iphone_project
	members = john

Bây giờ thì John chỉ có thể clone và theo dõi các cập nhật, chứ Gitosis không cho phép anh ta push lên dự án đó. Bạn có thể tạo số lượng nhóm theo ý muốn, mỗi nhóm bao gồm các thành viên và dự án khác nhau. Bạn cũng có thể chỉ định một nhóm trong vai trò là một thành viên của nhóm khác (sử dụng tiền tố `@`), để tận dụng toàn bộ thành viên của nhóm đó:

	[group mobile_committers]
	members = scott josie jessica

	[group mobile]
	writable  = iphone_project
	members   = @mobile_committers

	[group mobile_2]
	writable  = another_iphone_project
	members   = @mobile_committers john

Nếu bạn gặp bất kỳ vấn đề gì thì việc thêm `loglevel=DEBUG` vào trong phần `[gitosis]` có thể sẽ hữu ích. Nếu như bạn bị mất quyền push vì lỡ push một cấu hình khác, bạn có thể sửa chữa lại bằng cách thay đổi tập tin `/home/git/.gitosis.conf` - tập tin mà Gitosis đọc các thông tin cấu hình. Mỗi lần push, Gitosis sẽ sử dụng tập tin `gitosis.conf` để đưa vào thư mục đó. Nếu bạn thay đổi tập tin đó thủ công, thì nó sẽ vẫn giữ nguyên trạng thái như vậy cho tới lần push tiếp theo.

## Gitolite ##

Phần này sẽ giới thiệu qua cho bạn về Gitolite, cũng như hướng dẫn cách cấu hình và cài đặt. Tuy nhiên, nó không thể thay thế được [tài liệu][gltoc] có sẵn của Gitolite. Đôi khi nội dung phần này cũng thay đổi, vậy nên có thể bạn muốn xem phiên bản mới nhất tại [đây][gldpg].

[gldpg]: http://sitaramc.github.com/gitolite/progit.html
[gltoc]: http://sitaramc.github.com/gitolite/master-toc.html

Gitolite là một lớp cấp phép nằm trên cùng của Git, phụ thuộc vào `sshd` và `httpd` để xác thực. (Tóm tắt: xác thực là nhận diện người dùng, cấp phép là quyết định xem người dùng đó có được quyền thực hiện một hành động nào đó hay không).

Gitolite không chỉ cho phép bạn chỉ định quyền theo kho chứa, mà còn cả nhánh hoặc thẻ (tag) trong mỗi kho chứa. Nghĩa là bạn có thể chỉ định người dùng (hoặc nhóm người dùng) chỉ được phép push vào các "refs" (nhánh hoặc thẻ) nhất định.

### Cài Đặt ###

Cài đặt Gitolite rất đơn giản, ngay cả khi bạn không đọc tài liệu hướng dẫn đi kèm. Bạn cần một tài khoản trên một máy chủ Unix nào đó. Bạn không cần có quyền root, giả sử là Git, Perl và một loại máy chủ SSH nào đó đã được cài đặt sẵn. Trong các ví dụ tiếp theo, chúng ta sẽ sử dụng tài khoản `git` trên máy chủ có tên `gitserver`.

Ở khía cạnh nào đó Gitolite có chút khác biệt với các "máy chủ" khác - truy cập thông qua SSH, do vậy mỗi userid trên máy chủ có thể (potential) đóng vai trò là một "máy chủ Gitolite" (Gitolite host). Chúng ta sẽ sử dụng phương pháp cài đặt đơn giản nhất trong phần này; vui lòng xem các phương pháp cài đặt khác trong tài liệu hướng dẫn (documentation).

Để bắt đầu, hãy tạo mới một tài khoản người dùng có tên `git` trên máy chủ và đăng nhập sử dụng tài khoản này. Copy khóa SSH công khai (tập tin có tên `~/.ssh/id_rsa.pub` nếu bạn chạy `ssh-keygen` với các cài đặt mặc định) từ máy của bạn, đổi tên nó thành `<tenban>.pub` (chúng ta sẽ sử dụng `scott.pub` trong các ví dụ này). Và sau đó chạy các lệnh sau:

	$ git clone git://github.com/sitaramc/gitolite
	$ gitolite/install -ln
	    # assumes $HOME/bin exists and is in your $PATH
	$ gitolite setup -pk $HOME/scott.pub

Câu lệnh cuối tạo mới một kho chứa Git có tên `gitolite-admin` trên máy chủ.

Cuối cùng, quay trở lại với máy của bạn, hãy chạy lệnh `git clone git@gitserver:gitolite-admin`. Vậy là bạn đã xong! Gitolite đã được cài đặt trên máy chủ, và bạn đã có một kho chứa mới là `gitolite-admin` trên máy của mình. Bạn quản lý các cấu hình Gitolite bằng cách thay đổi kho chứa này và push lên.

### Tuỳ Chỉnh Cài Đặt ###

Cài đặt mặc định thường nhanh và đơn giản dành cho đa số mọi người, ngoài ta còn một số cách để tủy chính nó nếu bạn muốn. Bạn có thể dễ dàng thực hiện các thay đổi bằng cách sửa tập tin rc, nếu như vậy vẫn chưa đủ thì bạn có thể đọc thêm tài liệu hướng dẫn về các cách tùy chỉnh Gitolite khác.

### Config File and Access Control Rules ###

Sau khi quá trình cài đặt hoàn tất, bạn có thể duyệt qua kho chứa `gitolute-admin` mà bạn vừa clone để xem bạn có những gì trong đó:

	$ cd ~/gitolite-admin/
	$ ls
	conf/  keydir/
	$ find conf keydir -type f
	conf/gitolite.conf
	keydir/scott.pub
	$ cat conf/gitolite.conf

	repo gitolite-admin
	    RW+                 = scott

	repo testing
	    RW+                 = @all

Lưu tài khoản "scott" (tên của khóa công khai trong lệnh `gitolite setup` bạn sử dụng trước đó) có quyền đọc-ghi trên cả kho chứa `gitolite-admin` này cũng như khóa công khai cùng tên.

Thêm người dùng mới khá đơn giản. Để thêm một tài khoản mới có tên "alice", bạn cần khóa công khai của cô ấy, đổi tên nó thành `alice.pub`, và sau đó đặt nó vào trong thư mục `keydir` của kho chứa `gitolite-admin` bạn đã clone về máy của bạn. Sử dụng lệnh add, commit, và sau đó là push lên máy chủ, vậy là bạn đã có thêm một người dùng mới.

Cú pháp cho tập tin cấu hình của Gitolite diễn giải rất chi tiết, vì thế tôi chỉ đề cập đến các vấn đề đáng chú ý ở đây.

Để thuận lợi thì bạn có thể nhóm các kho chứa hoặc người dùng lại với nhau. Tên các nhóm cũng giống như các macro; chúng không nhất thiết phải là các dự án hay người dùng; sự khác nhau chỉ rõ rệt khi bạn *sử dụng* các "macro" đó.

	@oss_repos      = linux perl rakudo git gitolite
	@secret_repos   = fenestra pear

	@admins         = scott
	@interns        = ashok
	@engineers      = sitaram dilbert wally alice
	@staff          = @admins @engineers @interns

Bạn có thể quản lý các quyền ở mức độ "ref". Trong ví dụ sau, các thực tập sinh chỉ có thể push vào nhánh "int". Các kỹ sư có thể push vào bất kỳ nhánh nào có tên bắt đầu bằng "eng-", và các tag bắt đầu bằng "rc" theo sau là một số. Và các quản trị viên có thể làm bất kỳ việc gì (bao gồm cả phục hồi) trên bất kỳ ref nào.

	repo @oss_repos
	    RW  int$                = @interns
	    RW  eng-                = @engineers
	    RW  refs/tags/rc[0-9]   = @engineers
	    RW+                     = @admins

Biểu thức sau `RW` hoặc `RW+` là một biểu thức chính quy (regex) dùng để so sánh các refname (ref) sẽ được push lên. Do vậy chúng được gọi là "refex"! Tất nhiên, một refex có thể phức tạp và hiệu lực lớn hơn nhiều so với trong ví dụ này, vì thế bạn không nên lạm dụng quá nếu như bạn chưa thực sự thành thạo Perl regex.



Also, as you probably guessed, Gitolite prefixes `refs/heads/` as a syntactic convenience if the refex does not begin with `refs/`.

An important feature of the config file’s syntax is that all the rules for a repository need not be in one place.  You can keep all the common stuff together, like the rules for all `oss_repos` shown above, then add specific rules for specific cases later on, like so:

	repo gitolite
	    RW+                     = sitaram

That rule will just get added to the ruleset for the `gitolite` repository.

At this point you might be wondering how the access control rules are actually applied, so let’s go over that briefly.

There are two levels of access control in Gitolite.  The first is at the repository level; if you have read (or write) access to *any* ref in the repository, then you have read (or write) access to the repository.  This is the only access control that Gitosis had.

The second level, applicable only to "write" access, is by branch or tag within a repository.  The username, the access being attempted (`W` or `+`), and the refname being updated are known.  The access rules are checked in order of appearance in the config file, looking for a match for this combination (but remember that the refname is regex-matched, not merely string-matched).  If a match is found, the push succeeds.  A fallthrough results in access being denied.

### Advanced Access Control with "deny" rules ###

So far, we’ve only seen permissions to be one of `R`, `RW`, or `RW+`.  However, Gitolite allows another permission: `-`, standing for "deny".  This gives you a lot more power, at the expense of some complexity, because now fallthrough is not the *only* way for access to be denied, so the *order of the rules now matters*!

Let us say, in the situation above, we want engineers to be able to rewind any branch *except* master and integ.  Here’s how to do that:

	    RW  master integ    = @engineers
	    -   master integ    = @engineers
	    RW+                 = @engineers

Again, you simply follow the rules top down until you hit a match for your access mode, or a deny.  Non-rewind push to master or integ is allowed by the first rule.  A rewind push to those refs does not match the first rule, drops down to the second, and is therefore denied.  Any push (rewind or non-rewind) to refs other than master or integ won’t match the first two rules anyway, and the third rule allows it.

### Restricting pushes by files changed ###

In addition to restricting what branches a user can push changes to, you can also restrict what files they are allowed to touch.  For example, perhaps the Makefile (or some other program) is really not supposed to be changed by just anyone, because a lot of things depend on it or would break if the changes are not done *just right*.  You can tell Gitolite:

    repo foo
        RW                      =   @junior_devs @senior_devs

        -   VREF/NAME/Makefile  =   @junior_devs

User who are migrating from the older Gitolite should note that there is a significant change in behaviour with regard to this feature; please see the migration guide for details.

### Personal Branches ###

Gitolite also has a feature called "personal branches" (or rather, "personal branch namespace") that can be very useful in a corporate environment.

A lot of code exchange in the Git world happens by "please pull" requests.  In a corporate environment, however, unauthenticated access is a no-no, and a developer workstation cannot do authentication, so you have to push to the central server and ask someone to pull from there.

This would normally cause the same branch name clutter as in a centralised VCS, plus setting up permissions for this becomes a chore for the admin.

Gitolite lets you define a "personal" or "scratch" namespace prefix for each developer (for example, `refs/personal/<devname>/*`); please see the documentation for details.

### "Wildcard" repositories ###

Gitolite allows you to specify repositories with wildcards (actually Perl regexes), like, for example `assignments/s[0-9][0-9]/a[0-9][0-9]`, to pick a random example.  It also allows you to assign a new permission mode (`C`) which enables users to create repositories based on such wild cards, automatically assigns ownership to the specific user who created it, allows him/her to hand out `R` and `RW` permissions to other users to collaborate, etc.  Again, please see the documentation for details.

### Other Features ###

We’ll round off this discussion with a sampling of other features, all of which, and many more, are described in great detail in the documentation.

**Logging**: Gitolite logs all successful accesses.  If you were somewhat relaxed about giving people rewind permissions (`RW+`) and some kid blew away `master`, the log file is a life saver, in terms of easily and quickly finding the SHA that got hosed.

**Access rights reporting**: Another convenient feature is what happens when you try and just ssh to the server.  Gitolite shows you what repos you have access to, and what that access may be.  Here’s an example:

        hello scott, this is git@git running gitolite3 v3.01-18-g9609868 on git 1.7.4.4

             R     anu-wsd
             R     entrans
             R  W  git-notes
             R  W  gitolite
             R  W  gitolite-admin
             R     indic_web_input
             R     shreelipi_converter

**Delegation**: For really large installations, you can delegate responsibility for groups of repositories to various people and have them manage those pieces independently.  This reduces the load on the main admin, and makes him less of a bottleneck.

**Mirroring**: Gitolite can help you maintain multiple mirrors, and switch between them easily if the primary server goes down.

## Git Daemon ##

For public, unauthenticated read access to your projects, you’ll want to move past the HTTP protocol and start using the Git protocol. The main reason is speed. The Git protocol is far more efficient and thus faster than the HTTP protocol, so using it will save your users time.

Again, this is for unauthenticated read-only access. If you’re running this on a server outside your firewall, it should only be used for projects that are publicly visible to the world. If the server you’re running it on is inside your firewall, you might use it for projects that a large number of people or computers (continuous integration or build servers) have read-only access to, when you don’t want to have to add an SSH key for each.

In any case, the Git protocol is relatively easy to set up. Basically, you need to run this command in a daemonized manner:

	git daemon --reuseaddr --base-path=/opt/git/ /opt/git/

`--reuseaddr` allows the server to restart without waiting for old connections to time out, the `--base-path` option allows people to clone projects without specifying the entire path, and the path at the end tells the Git daemon where to look for repositories to export. If you’re running a firewall, you’ll also need to punch a hole in it at port 9418 on the box you’re setting this up on.

You can daemonize this process a number of ways, depending on the operating system you’re running. On an Ubuntu machine, you use an Upstart script. So, in the following file

	/etc/event.d/local-git-daemon

you put this script:

	start on startup
	stop on shutdown
	exec /usr/bin/git daemon \
	    --user=git --group=git \
	    --reuseaddr \
	    --base-path=/opt/git/ \
	    /opt/git/
	respawn

For security reasons, it is strongly encouraged to have this daemon run as a user with read-only permissions to the repositories — you can easily do this by creating a new user 'git-ro' and running the daemon as them.  For the sake of simplicity we’ll simply run it as the same 'git' user that Gitosis is running as.

When you restart your machine, your Git daemon will start automatically and respawn if it goes down. To get it running without having to reboot, you can run this:

	initctl start local-git-daemon

On other systems, you may want to use `xinetd`, a script in your `sysvinit` system, or something else — as long as you get that command daemonized and watched somehow.

Next, you have to tell your Gitosis server which repositories to allow unauthenticated Git server-based access to. If you add a section for each repository, you can specify the ones from which you want your Git daemon to allow reading. If you want to allow Git protocol access for the `iphone_project`, you add this to the end of the `gitosis.conf` file:

	[repo iphone_project]
	daemon = yes

When that is committed and pushed up, your running daemon should start serving requests for the project to anyone who has access to port 9418 on your server.

If you decide not to use Gitosis, but you want to set up a Git daemon, you’ll have to run this on each project you want the Git daemon to serve:

	$ cd /path/to/project.git
	$ touch git-daemon-export-ok

The presence of that file tells Git that it’s OK to serve this project without authentication.

Gitosis can also control which projects GitWeb shows. First, you need to add something like the following to the `/etc/gitweb.conf` file:

	$projects_list = "/home/git/gitosis/projects.list";
	$projectroot = "/home/git/repositories";
	$export_ok = "git-daemon-export-ok";
	@git_base_url_list = ('git://gitserver');

You can control which projects GitWeb lets users browse by adding or removing a `gitweb` setting in the Gitosis configuration file. For instance, if you want the `iphone_project` to show up on GitWeb, you make the `repo` setting look like this:

	[repo iphone_project]
	daemon = yes
	gitweb = yes

Now, if you commit and push the project, GitWeb will automatically start showing the `iphone_project`.

## Hosted Git ##

If you don’t want to go through all of the work involved in setting up your own Git server, you have several options for hosting your Git projects on an external dedicated hosting site. Doing so offers a number of advantages: a hosting site is generally quick to set up and easy to start projects on, and no server maintenance or monitoring is involved. Even if you set up and run your own server internally, you may still want to use a public hosting site for your open source code — it’s generally easier for the open source community to find and help you with.

These days, you have a huge number of hosting options to choose from, each with different advantages and disadvantages. To see an up-to-date list, check out the following page:

	https://git.wiki.kernel.org/index.php/GitHosting

Because we can’t cover all of them, and because I happen to work at one of them, we’ll use this section to walk through setting up an account and creating a new project at GitHub. This will give you an idea of what is involved.

GitHub is by far the largest open source Git hosting site and it’s also one of the very few that offers both public and private hosting options so you can keep your open source and private commercial code in the same place. In fact, we used GitHub to privately collaborate on this book.

### GitHub ###

GitHub is slightly different than most code-hosting sites in the way that it namespaces projects. Instead of being primarily based on the project, GitHub is user centric. That means when I host my `grit` project on GitHub, you won’t find it at `github.com/grit` but instead at `github.com/schacon/grit`. There is no canonical version of any project, which allows a project to move from one user to another seamlessly if the first author abandons the project.

GitHub is also a commercial company that charges for accounts that maintain private repositories, but anyone can quickly get a free account to host as many open source projects as they want. We’ll quickly go over how that is done.

### Setting Up a User Account ###

The first thing you need to do is set up a free user account. If you visit the Pricing and Signup page at `http://github.com/plans` and click the "Sign Up" button on the Free account (see Figure 4-2), you’re taken to the signup page.

Insert 18333fig0402.png
Figure 4-2. The GitHub plan page.

Here you must choose a username that isn’t yet taken in the system and enter an e-mail address that will be associated with the account and a password (see Figure 4-3).

Insert 18333fig0403.png
Figure 4-3. The GitHub user signup form.

If you have it available, this is a good time to add your public SSH key as well. We covered how to generate a new key earlier, in the "Simple Setups" section. Take the contents of the public key of that pair, and paste it into the SSH Public Key text box. Clicking the "explain ssh keys" link takes you to detailed instructions on how to do so on all major operating systems.
Clicking the "I agree, sign me up" button takes you to your new user dashboard (see Figure 4-4).

Insert 18333fig0404.png
Figure 4-4. The GitHub user dashboard.

Next you can create a new repository.

### Creating a New Repository ###

Start by clicking the "create a new one" link next to Your Repositories on the user dashboard. You’re taken to the Create a New Repository form (see Figure 4-5).

Insert 18333fig0405.png
Figure 4-5. Creating a new repository on GitHub.

All you really have to do is provide a project name, but you can also add a description. When that is done, click the "Create Repository" button. Now you have a new repository on GitHub (see Figure 4-6).

Insert 18333fig0406.png
Figure 4-6. GitHub project header information.

Since you have no code there yet, GitHub will show you instructions for how create a brand-new project, push an existing Git project up, or import a project from a public Subversion repository (see Figure 4-7).

Insert 18333fig0407.png
Figure 4-7. Instructions for a new repository.

These instructions are similar to what we’ve already gone over. To initialize a project if it isn’t already a Git project, you use

	$ git init
	$ git add .
	$ git commit -m 'initial commit'

When you have a Git repository locally, add GitHub as a remote and push up your master branch:

	$ git remote add origin git@github.com:testinguser/iphone_project.git
	$ git push origin master

Now your project is hosted on GitHub, and you can give the URL to anyone you want to share your project with. In this case, it’s `http://github.com/testinguser/iphone_project`. You can also see from the header on each of your project’s pages that you have two Git URLs (see Figure 4-8).

Insert 18333fig0408.png
Figure 4-8. Project header with a public URL and a private URL.

The Public Clone URL is a public, read-only Git URL over which anyone can clone the project. Feel free to give out that URL and post it on your web site or what have you.

The Your Clone URL is a read/write SSH-based URL that you can read or write over only if you connect with the SSH private key associated with the public key you uploaded for your user. When other users visit this project page, they won’t see that URL—only the public one.

### Importing from Subversion ###

If you have an existing public Subversion project that you want to import into Git, GitHub can often do that for you. At the bottom of the instructions page is a link to a Subversion import. If you click it, you see a form with information about the import process and a text box where you can paste in the URL of your public Subversion project (see Figure 4-9).

Insert 18333fig0409.png
Figure 4-9. Subversion importing interface.

If your project is very large, nonstandard, or private, this process probably won’t work for you. In Chapter 7, you’ll learn how to do more complicated manual project imports.

### Adding Collaborators ###

Let’s add the rest of the team. If John, Josie, and Jessica all sign up for accounts on GitHub, and you want to give them push access to your repository, you can add them to your project as collaborators. Doing so will allow pushes from their public keys to work.

Click the "edit" button in the project header or the Admin tab at the top of the project to reach the Admin page of your GitHub project (see Figure 4-10).

Insert 18333fig0410.png
Figure 4-10. GitHub administration page.

To give another user write access to your project, click the “Add another collaborator” link. A new text box appears, into which you can type a username. As you type, a helper pops up, showing you possible username matches. When you find the correct user, click the Add button to add that user as a collaborator on your project (see Figure 4-11).

Insert 18333fig0411.png
Figure 4-11. Adding a collaborator to your project.

When you’re finished adding collaborators, you should see a list of them in the Repository Collaborators box (see Figure 4-12).

Insert 18333fig0412.png
Figure 4-12. A list of collaborators on your project.

If you need to revoke access to individuals, you can click the "revoke" link, and their push access will be removed. For future projects, you can also copy collaborator groups by copying the permissions of an existing project.

### Your Project ###

After you push your project up or have it imported from Subversion, you have a main project page that looks something like Figure 4-13.

Insert 18333fig0413.png
Figure 4-13. A GitHub main project page.

When people visit your project, they see this page. It contains tabs to different aspects of your projects. The Commits tab shows a list of commits in reverse chronological order, similar to the output of the `git log` command. The Network tab shows all the people who have forked your project and contributed back. The Downloads tab allows you to upload project binaries and link to tarballs and zipped versions of any tagged points in your project. The Wiki tab provides a wiki where you can write documentation or other information about your project. The Graphs tab has some contribution visualizations and statistics about your project. The main Source tab that you land on shows your project’s main directory listing and automatically renders the README file below it if you have one. This tab also shows a box with the latest commit information.

### Forking Projects ###

If you want to contribute to an existing project to which you don’t have push access, GitHub encourages forking the project. When you land on a project page that looks interesting and you want to hack on it a bit, you can click the "fork" button in the project header to have GitHub copy that project to your user so you can push to it.

This way, projects don’t have to worry about adding users as collaborators to give them push access. People can fork a project and push to it, and the main project maintainer can pull in those changes by adding them as remotes and merging in their work.

To fork a project, visit the project page (in this case, mojombo/chronic) and click the "fork" button in the header (see Figure 4-14).

Insert 18333fig0414.png
Figure 4-14. Get a writable copy of any repository by clicking the "fork" button.

After a few seconds, you’re taken to your new project page, which indicates that this project is a fork of another one (see Figure 4-15).

Insert 18333fig0415.png
Figure 4-15. Your fork of a project.

### GitHub Summary ###

That’s all we’ll cover about GitHub, but it’s important to note how quickly you can do all this. You can create an account, add a new project, and push to it in a matter of minutes. If your project is open source, you also get a huge community of developers who now have visibility into your project and may well fork it and help contribute to it. At the very least, this may be a way to get up and running with Git and try it out quickly.

## Summary ##

You have several options to get a remote Git repository up and running so that you can collaborate with others or share your work.

Running your own server gives you a lot of control and allows you to run the server within your own firewall, but such a server generally requires a fair amount of your time to set up and maintain. If you place your data on a hosted server, it’s easy to set up and maintain; however, you have to be able to keep your code on someone else’s servers, and some organizations don’t allow that.

It should be fairly straightforward to determine which solution or combination of solutions is appropriate for you and your organization.

### **1. Microservices**

Trong nhiều năm qua, chúng ta đã tìm ra những cách tốt hơn để xây dựng hệ thống. Chúng ta đã học hỏi từ những gì đã có trước đây, áp dụng các công nghệ mới, và quan sát cách một làn sóng các công ty công nghệ mới vận hành theo những cách khác nhau để tạo ra các hệ thống CNTT giúp cả khách hàng và lập trình viên của họ hài lòng hơn.

Cuốn sách *Domain-Driven Design* (Thiết kế Hướng Miền) của Eric Evans (Addison-Wesley) đã giúp chúng ta hiểu tầm quan trọng của việc thể hiện thế giới thực trong mã nguồn và chỉ ra những cách tốt hơn để mô hình hóa hệ thống. Khái niệm về phân phối liên tục (continuous delivery) đã cho thấy cách chúng ta có thể đưa phần mềm vào sản xuất một cách hiệu quả và hiệu suất hơn, thấm nhuần trong chúng ta ý tưởng rằng mỗi lần check-in đều nên được xem như một ứng cử viên phát hành. Sự hiểu biết của chúng ta về cách Web hoạt động đã dẫn đến việc phát triển các cách tốt hơn để các máy móc giao tiếp với nhau. Khái niệm kiến trúc lục giác (hexagonal architecture) của Alistair Cockburn đã dẫn dắt chúng ta thoát khỏi các kiến trúc phân tầng nơi logic nghiệp vụ có thể bị ẩn giấu. Các nền tảng ảo hóa cho phép chúng ta cấp phát và thay đổi kích thước máy móc theo ý muốn, với tự động hóa cơ sở hạ tầng (infrastructure automation) cho chúng ta cách xử lý các máy này ở quy mô lớn. Một số tổ chức lớn và thành công như Amazon và Google đã ủng hộ quan điểm về các đội nhỏ sở hữu toàn bộ vòng đời của dịch vụ của họ. Và gần đây hơn, Netflix đã chia sẻ với chúng ta cách xây dựng các hệ thống chống mong manh (antifragile systems) ở một quy mô mà khó có thể tưởng tượng được chỉ 10 năm trước.

Thiết kế hướng miền. Phân phối liên tục. Ảo hóa theo yêu cầu. Tự động hóa cơ sở hạ tầng. Các đội tự chủ nhỏ. Hệ thống ở quy mô lớn. Microservices đã nổi lên từ thế giới này. Chúng không được phát minh hay mô tả trước khi có thực tế; chúng nổi lên như một xu hướng, hoặc một mẫu hình, từ việc sử dụng trong thế giới thực. Nhưng chúng chỉ tồn tại nhờ tất cả những gì đã đi trước. Trong suốt cuốn sách này, tôi sẽ rút ra những sợi dây từ công việc trước đây này để giúp vẽ nên một bức tranh về cách xây dựng, quản lý và phát triển microservices.

Nhiều tổ chức đã nhận thấy rằng bằng cách áp dụng các kiến trúc microservice, chi tiết và mịn, họ có thể cung cấp phần mềm nhanh hơn và áp dụng các công nghệ mới hơn. Microservices cho chúng ta nhiều tự do hơn đáng kể để phản ứng và đưa ra các quyết định khác nhau, cho phép chúng ta phản hồi nhanh hơn với sự thay đổi không thể tránh khỏi tác động đến tất cả chúng ta.

#### **Microservices là gì?**

Microservices là các dịch vụ nhỏ, tự chủ hoạt động cùng nhau. Hãy phân tích định nghĩa đó một chút và xem xét các đặc điểm làm cho microservices khác biệt.

##### **Nhỏ, và Tập trung làm tốt một việc duy nhất**

Codebase phát triển khi chúng ta viết mã để thêm các tính năng mới. Theo thời gian, có thể khó biết được cần phải thay đổi ở đâu vì codebase quá lớn. Mặc dù có nỗ lực hướng tới các codebase nguyên khối (monolithic) rõ ràng, theo module, nhưng quá thường xuyên, các ranh giới tùy ý trong tiến trình này bị phá vỡ. Mã liên quan đến các chức năng tương tự bắt đầu lan rộng khắp nơi, làm cho việc sửa lỗi hoặc triển khai trở nên khó khăn hơn.

Trong một hệ thống nguyên khối, chúng ta chống lại các lực lượng này bằng cách cố gắng đảm bảo mã của chúng ta có tính gắn kết (cohesive) hơn, thường bằng cách tạo ra các trừu tượng hoặc module. **Tính gắn kết (Cohesion)**—động lực để nhóm các mã liên quan lại với nhau—là một khái niệm quan trọng khi chúng ta nghĩ về microservices. Điều này được củng cố bởi định nghĩa của Robert C. Martin về **Nguyên lý Trách nhiệm Đơn nhất (Single Responsibility Principle)**, trong đó nêu rõ "Hãy tập hợp những thứ thay đổi vì cùng một lý do, và tách biệt những thứ thay đổi vì những lý do khác nhau."

Microservices áp dụng cách tiếp cận tương tự cho các dịch vụ độc lập. Chúng ta tập trung ranh giới dịch vụ của mình vào các ranh giới nghiệp vụ, làm cho rõ ràng nơi mã nguồn tồn tại cho một chức năng nhất định. Và bằng cách giữ cho dịch vụ này tập trung vào một ranh giới rõ ràng, chúng ta tránh được cám dỗ để nó phát triển quá lớn, với tất cả những khó khăn liên quan mà điều này có thể gây ra.

Câu hỏi tôi thường được hỏi là *nhỏ là bao nhiêu?* Đưa ra một con số cho số dòng mã là có vấn đề, vì một số ngôn ngữ biểu cảm hơn các ngôn ngữ khác và do đó có thể làm được nhiều việc hơn trong ít dòng mã hơn. Chúng ta cũng phải xem xét thực tế rằng chúng ta có thể đang kéo vào nhiều phụ thuộc (dependencies), mà bản thân chúng chứa nhiều dòng mã. Ngoài ra, một số phần của miền nghiệp vụ của bạn có thể thực sự phức tạp, đòi hỏi nhiều mã hơn. Jon Eaves tại RealEstate.com.au ở Úc mô tả một microservice là thứ có thể được viết lại trong hai tuần, một quy tắc kinh nghiệm có ý nghĩa đối với bối cảnh cụ thể của ông.

Một câu trả lời hơi sáo rỗng khác mà tôi có thể đưa ra là *đủ nhỏ và không nhỏ hơn*. Khi phát biểu tại các hội nghị, tôi gần như luôn hỏi câu hỏi *ai có một hệ thống quá lớn và bạn muốn chia nhỏ nó ra?* Gần như mọi người đều giơ tay. Chúng ta dường như có một cảm giác rất tốt về những gì là quá lớn, và do đó có thể lập luận rằng một khi một đoạn mã không còn cảm thấy quá lớn, nó có lẽ đã đủ nhỏ.

Một yếu tố mạnh mẽ giúp chúng ta trả lời *nhỏ là bao nhiêu?* là dịch vụ đó phù hợp với cấu trúc đội nhóm như thế nào. Nếu codebase quá lớn để được quản lý bởi một đội nhỏ, việc tìm cách chia nhỏ nó là rất hợp lý. Chúng ta sẽ nói thêm về sự phù hợp với tổ chức sau này.

Khi nói đến việc nhỏ là đủ nhỏ, tôi thích nghĩ theo các thuật ngữ sau: dịch vụ càng nhỏ, bạn càng tối đa hóa được lợi ích và nhược điểm của kiến trúc microservice. Khi bạn thu nhỏ hơn, lợi ích về sự phụ thuộc lẫn nhau tăng lên. Nhưng cũng vậy, một số sự phức tạp nảy sinh từ việc có nhiều bộ phận chuyển động hơn, điều mà chúng ta sẽ khám phá trong suốt cuốn sách này. Khi bạn xử lý sự phức tạp này tốt hơn, bạn có thể phấn đấu cho các dịch vụ ngày càng nhỏ hơn.

##### **Tự chủ (Autonomous)**

Microservice của chúng ta là một thực thể riêng biệt. Nó có thể được triển khai như một dịch vụ biệt lập trên một nền tảng dịch vụ (Platform as a Service - PaaS), hoặc nó có thể là một tiến trình hệ điều hành riêng. Chúng ta cố gắng tránh đóng gói nhiều dịch vụ vào cùng một máy, mặc dù định nghĩa về *máy* trong thế giới ngày nay khá mơ hồ! Như chúng ta sẽ thảo luận sau, mặc dù sự cô lập này có thể thêm một số chi phí, nhưng sự đơn giản kết quả làm cho hệ thống phân tán của chúng ta dễ dàng hơn nhiều để lý giải, và các công nghệ mới hơn có thể giảm thiểu nhiều thách thức liên quan đến hình thức triển khai này.

Tất cả giao tiếp giữa các dịch vụ đều thông qua các cuộc gọi mạng, để thực thi sự tách biệt giữa các dịch vụ và tránh những nguy cơ của việc ghép nối chặt chẽ (tight coupling).

Các dịch vụ này cần có khả năng thay đổi độc lập với nhau, và được triển khai một mình mà không yêu cầu người tiêu dùng thay đổi. Chúng ta cần suy nghĩ về những gì dịch vụ của chúng ta nên phơi bày ra ngoài, và những gì chúng nên cho phép ẩn đi. Nếu có quá nhiều sự chia sẻ, các dịch vụ tiêu thụ của chúng ta sẽ bị ghép nối với các biểu diễn nội bộ của chúng ta. Điều này làm giảm quyền tự chủ của chúng ta, vì nó đòi hỏi sự phối hợp bổ sung với người tiêu dùng khi thực hiện thay đổi.

Dịch vụ của chúng ta phơi bày một giao diện lập trình ứng dụng (API), và các dịch vụ hợp tác giao tiếp với chúng ta thông qua các API đó. Chúng ta cũng cần suy nghĩ về công nghệ nào là phù hợp để đảm bảo rằng bản thân nó không ghép nối người tiêu dùng. Điều này có thể có nghĩa là chọn các API không phụ thuộc vào công nghệ để đảm bảo rằng chúng ta không hạn chế các lựa chọn công nghệ. Chúng ta sẽ quay lại nhiều lần tầm quan trọng của các API tốt, được tách rời trong suốt cuốn sách này.

Nếu không có sự tách rời, mọi thứ sẽ sụp đổ. Quy tắc vàng: *bạn có thể thực hiện một thay đổi cho một dịch vụ và tự triển khai nó mà không thay đổi bất cứ điều gì khác không?* Nếu câu trả lời là không, thì nhiều lợi thế mà chúng ta thảo luận trong suốt cuốn sách này sẽ khó đạt được.

Để tách rời tốt, bạn sẽ cần mô hình hóa dịch vụ của mình đúng cách và có được các API đúng cách. Tôi sẽ nói rất nhiều về điều đó.

#### **Những lợi ích chính (Key Benefits)**

Lợi ích của microservices rất nhiều và đa dạng. Nhiều lợi ích trong số này cũng là đặc điểm chung của bất kỳ hệ thống phân tán nào. Tuy nhiên, microservices có xu hướng đạt được những lợi ích này ở mức độ cao hơn, chủ yếu là do cách chúng khai thác sâu các khái niệm nền tảng của hệ thống phân tán và kiến trúc hướng dịch vụ.

##### **Đa dạng về Công nghệ (Technology Heterogeneity)**

Với một hệ thống bao gồm nhiều dịch vụ hợp tác, chúng ta có thể quyết định sử dụng các công nghệ khác nhau bên trong mỗi dịch vụ. Điều này cho phép chúng ta chọn công cụ phù hợp cho từng công việc, thay vì phải chọn một cách tiếp cận "một kích cỡ cho tất cả" được chuẩn hóa, mà thường kết thúc là giải pháp kém tối ưu nhất (mẫu số chung thấp nhất).

Nếu một phần của hệ thống cần cải thiện hiệu suất, chúng ta có thể quyết định sử dụng một ngăn xếp công nghệ (technology stack) khác có khả năng đạt được các mức hiệu suất yêu cầu tốt hơn. Chúng ta cũng có thể quyết định rằng cách chúng ta lưu trữ dữ liệu cần thay đổi cho các phần khác nhau của hệ thống. Ví dụ, đối với một mạng xã hội, chúng ta có thể lưu trữ các tương tác của người dùng trong một cơ sở dữ liệu hướng đồ thị (graph-oriented database) để phản ánh bản chất kết nối chặt chẽ của một đồ thị xã hội, nhưng có lẽ các bài đăng mà người dùng tạo ra có thể được lưu trữ trong một kho dữ liệu hướng tài liệu (document-oriented data store), tạo ra một kiến trúc đa dạng như trong Hình 1-1.

![alt text](<images/Screenshot from 2025-09-29 06-48-03.png>)

**Hình 1-1.** *Microservices có thể cho phép bạn dễ dàng áp dụng các công nghệ khác nhau hơn*

Với microservices, chúng ta cũng có thể áp dụng công nghệ nhanh hơn và hiểu được những tiến bộ mới có thể giúp ích cho chúng ta như thế nào. Một trong những rào cản lớn nhất để thử nghiệm và áp dụng công nghệ mới là những rủi ro liên quan. Với một ứng dụng nguyên khối, nếu tôi muốn thử một ngôn ngữ lập trình, cơ sở dữ liệu hoặc framework mới, bất kỳ thay đổi nào cũng sẽ tác động đến một phần lớn hệ thống của tôi. Với một hệ thống bao gồm nhiều dịch vụ, tôi có nhiều nơi mới để thử nghiệm một công nghệ mới. Tôi có thể chọn một dịch vụ có rủi ro thấp nhất và sử dụng công nghệ mới ở đó, biết rằng tôi có thể hạn chế mọi tác động tiêu cực tiềm tàng. Nhiều tổ chức nhận thấy khả năng hấp thụ công nghệ mới nhanh hơn này là một lợi thế thực sự cho họ.

##### **Khả năng phục hồi (Resilience)**

Một khái niệm quan trọng trong kỹ thuật phục hồi là **vách ngăn (bulkhead)**. Nếu một thành phần của hệ thống bị lỗi, nhưng lỗi đó không lan truyền (cascade), bạn có thể cô lập vấn đề và phần còn lại của hệ thống vẫn có thể tiếp tục hoạt động. Ranh giới dịch vụ trở thành những vách ngăn rõ ràng của bạn. Trong một dịch vụ nguyên khối, nếu dịch vụ bị lỗi, mọi thứ sẽ ngừng hoạt động. Với một hệ thống nguyên khối, chúng ta có thể chạy trên nhiều máy để giảm khả năng xảy ra lỗi, nhưng với microservices, chúng ta có thể xây dựng các hệ thống xử lý được sự cố hoàn toàn của các dịch vụ và **giảm cấp chức năng (degrade functionality)** một cách tương ứng.

Tuy nhiên, chúng ta cần phải cẩn thận. Để đảm bảo các hệ thống microservice của chúng ta có thể thực sự tận dụng được khả năng phục hồi được cải thiện này, chúng ta cần hiểu các nguồn lỗi mới mà các hệ thống phân tán phải đối mặt. Mạng có thể và sẽ bị lỗi, máy móc cũng vậy. Chúng ta cần biết cách xử lý điều này, và tác động (nếu có) của nó đối với người dùng cuối của phần mềm của chúng ta.

Chúng ta sẽ nói nhiều hơn về cách xử lý khả năng phục hồi tốt hơn và cách xử lý các chế độ lỗi trong **Chương 11**.

##### **Khả năng mở rộng (Scaling)**

Với một dịch vụ nguyên khối lớn, chúng ta phải mở rộng quy mô của mọi thứ cùng nhau. Một phần nhỏ của toàn bộ hệ thống của chúng ta bị hạn chế về hiệu suất, nhưng nếu hành vi đó bị khóa trong một ứng dụng nguyên khối khổng lồ, chúng ta phải xử lý việc mở rộng mọi thứ như một khối thống nhất. Với các dịch vụ nhỏ hơn, chúng ta chỉ cần mở rộng quy mô của những dịch vụ cần thiết, cho phép chúng ta chạy các phần khác của hệ thống trên phần cứng nhỏ hơn, ít mạnh mẽ hơn, như trong Hình 1-2.

![alt text](<images/Screenshot from 2025-09-29 06-48-12.png>)

**Hình 1-2.** *Bạn có thể nhắm mục tiêu mở rộng quy mô chỉ vào những microservice cần nó*

Gilt, một nhà bán lẻ thời trang trực tuyến, đã áp dụng microservices chính vì lý do này. Bắt đầu vào năm 2007 với một ứng dụng Rails nguyên khối, đến năm 2009, hệ thống của Gilt đã không thể đối phó với tải trọng đặt lên nó. Bằng cách tách các phần cốt lõi của hệ thống ra, Gilt đã có thể đối phó tốt hơn với các đợt tăng đột biến lưu lượng truy cập và ngày nay có hơn 450 microservice, mỗi microservice chạy trên nhiều máy riêng biệt.

Khi áp dụng các hệ thống cấp phát theo yêu cầu như những hệ thống được cung cấp bởi Amazon Web Services, chúng ta thậm chí có thể áp dụng việc mở rộng quy mô này theo yêu cầu cho những phần cần nó. Điều này cho phép chúng ta kiểm soát chi phí hiệu quả hơn. Không thường xuyên mà một cách tiếp cận kiến trúc lại có thể liên quan chặt chẽ đến việc tiết kiệm chi phí gần như ngay lập tức như vậy.

##### **Dễ dàng triển khai (Ease of Deployment)**

Một thay đổi một dòng trong một ứng dụng nguyên khối dài hàng triệu dòng đòi hỏi toàn bộ ứng dụng phải được triển khai để phát hành thay đổi đó. Đó có thể là một đợt triển khai có tác động lớn, rủi ro cao. Trong thực tế, các đợt triển khai có tác động lớn, rủi ro cao cuối cùng lại xảy ra không thường xuyên do sự sợ hãi có thể hiểu được. Thật không may, điều này có nghĩa là các thay đổi của chúng ta tích tụ và tích tụ giữa các bản phát hành, cho đến khi phiên bản mới của ứng dụng của chúng ta được đưa vào sản xuất có hàng loạt thay đổi. Và delta giữa các bản phát hành càng lớn, rủi ro chúng ta sẽ làm sai điều gì đó càng cao!

Với microservices, chúng ta có thể thực hiện một thay đổi cho một dịch vụ duy nhất và triển khai nó độc lập với phần còn lại của hệ thống. Điều này cho phép chúng ta đưa mã của mình ra triển khai nhanh hơn. Nếu một vấn đề xảy ra, nó có thể được cô lập nhanh chóng vào một dịch vụ riêng lẻ, giúp việc rollback nhanh chóng dễ dàng đạt được. Nó cũng có nghĩa là chúng ta có thể đưa chức năng mới của mình đến tay khách hàng nhanh hơn. Đây là một trong những lý do chính tại sao các tổ chức như Amazon và Netflix sử dụng các kiến trúc này—để đảm bảo họ loại bỏ càng nhiều trở ngại càng tốt để đưa phần mềm ra ngoài.

Công nghệ trong lĩnh vực này đã thay đổi rất nhiều trong vài năm qua, và chúng ta sẽ xem xét sâu hơn về chủ đề triển khai trong thế giới microservice trong **Chương 6**.

##### **Phù hợp với Tổ chức (Organizational Alignment)**

Nhiều người trong chúng ta đã trải qua các vấn đề liên quan đến các đội lớn và codebase lớn. Những vấn đề này có thể trở nên trầm trọng hơn khi đội ngũ bị phân tán. Chúng ta cũng biết rằng các đội nhỏ hơn làm việc trên các codebase nhỏ hơn có xu hướng hiệu quả hơn.

Microservices cho phép chúng ta sắp xếp kiến trúc của mình tốt hơn với tổ chức, giúp chúng ta giảm thiểu số lượng người làm việc trên bất kỳ một codebase nào để đạt được điểm tối ưu về quy mô đội và năng suất. Chúng ta cũng có thể chuyển quyền sở hữu dịch vụ giữa các đội để cố gắng giữ cho những người làm việc trên một dịch vụ được đặt cùng một chỗ. Chúng ta sẽ đi sâu hơn vào chủ đề này khi thảo luận về luật Conway trong **Chương 10**.

##### **Khả năng kết hợp (Composability)**

Một trong những lời hứa quan trọng của các hệ thống phân tán và kiến trúc hướng dịch vụ là chúng ta mở ra cơ hội tái sử dụng chức năng. Với microservices, chúng ta cho phép chức năng của mình được tiêu thụ theo những cách khác nhau cho các mục đích khác nhau. Điều này có thể đặc biệt quan trọng khi chúng ta nghĩ về cách người tiêu dùng sử dụng phần mềm của chúng ta. Đã qua rồi cái thời mà chúng ta có thể suy nghĩ hạn hẹp về trang web máy tính để bàn hoặc ứng dụng di động của mình. Bây giờ chúng ta cần suy nghĩ về vô số cách mà chúng ta có thể muốn kết hợp các khả năng cho Web, ứng dụng gốc, web di động, ứng dụng máy tính bảng hoặc thiết bị đeo được. Khi các tổ chức chuyển từ việc suy nghĩ theo các kênh hẹp sang các khái niệm toàn diện hơn về sự tương tác của khách hàng, chúng ta cần các kiến trúc có thể theo kịp.

Với microservices, hãy nghĩ rằng chúng ta đang mở ra các đường nối (seams) trong hệ thống của mình mà các bên ngoài có thể truy cập được. Khi hoàn cảnh thay đổi, chúng ta có thể xây dựng mọi thứ theo những cách khác nhau. Với một ứng dụng nguyên khối, tôi thường có một đường nối thô có thể được sử dụng từ bên ngoài. Nếu tôi muốn chia nhỏ nó ra để có được thứ gì đó hữu ích hơn, tôi sẽ cần một cái búa! Trong **Chương 5**, tôi sẽ thảo luận về các cách để bạn chia nhỏ các hệ thống nguyên khối hiện có, và hy vọng biến chúng thành một số microservice có thể tái sử dụng, có thể kết hợp lại.

##### **Tối ưu hóa cho khả năng thay thế (Optimizing for Replaceability)**

Nếu bạn làm việc tại một tổ chức cỡ vừa hoặc lớn hơn, rất có thể bạn biết về một hệ thống kế thừa (legacy system) lớn, khó chịu nào đó đang nằm trong góc. Hệ thống mà không ai muốn đụng vào. Hệ thống quan trọng đối với hoạt động của công ty bạn, nhưng lại được viết bằng một biến thể Fortran kỳ lạ nào đó và chỉ chạy trên phần cứng đã hết hạn sử dụng 25 năm trước. Tại sao nó chưa được thay thế? Bạn biết tại sao mà: đó là một công việc quá lớn và quá rủi ro.

Với các dịch vụ riêng lẻ của chúng ta có kích thước nhỏ, chi phí để thay thế chúng bằng một triển khai tốt hơn, hoặc thậm chí xóa chúng hoàn toàn, dễ quản lý hơn nhiều. Bạn đã bao nhiêu lần xóa hơn một trăm dòng mã trong một ngày mà không lo lắng quá nhiều về nó? Với các microservice thường có kích thước tương tự, rào cản để viết lại hoặc loại bỏ hoàn toàn các dịch vụ là rất thấp.

Các đội sử dụng phương pháp microservice cảm thấy thoải mái với việc viết lại hoàn toàn các dịch vụ khi cần thiết, và chỉ cần loại bỏ một dịch vụ khi nó không còn cần thiết nữa. Khi một codebase chỉ có vài trăm dòng, rất khó để mọi người trở nên gắn bó tình cảm với nó, và chi phí thay thế nó là khá nhỏ.

#### **Còn Kiến trúc Hướng dịch vụ (SOA) thì sao?**

**Kiến trúc Hướng dịch vụ (Service-Oriented Architecture - SOA)** là một phương pháp thiết kế nơi nhiều dịch vụ hợp tác để cung cấp một tập hợp các khả năng cuối cùng. Một dịch vụ ở đây thường có nghĩa là một tiến trình hệ điều hành hoàn toàn riêng biệt. Giao tiếp giữa các dịch vụ này diễn ra thông qua các cuộc gọi qua mạng thay vì các cuộc gọi phương thức trong ranh giới của một tiến trình.

SOA nổi lên như một cách tiếp cận để chống lại những thách thức của các ứng dụng `monolithic` lớn. Đó là một phương pháp nhằm thúc đẩy khả năng tái sử dụng phần mềm; ví dụ, hai hoặc nhiều ứng dụng người dùng cuối có thể cùng sử dụng các dịch vụ giống nhau. Nó nhằm mục đích làm cho việc bảo trì hoặc viết lại phần mềm dễ dàng hơn, vì về mặt lý thuyết, chúng ta có thể thay thế một dịch vụ bằng một dịch vụ khác mà không ai biết, miễn là ngữ nghĩa (semantics) của dịch vụ không thay đổi quá nhiều.

Về bản chất, SOA là một ý tưởng rất hợp lý. Tuy nhiên, mặc dù có nhiều nỗ lực, vẫn thiếu một sự đồng thuận tốt về cách thực hiện SOA tốt. Theo quan điểm của tôi, phần lớn ngành công nghiệp đã không nhìn nhận vấn đề một cách đủ toàn diện và không trình bày một giải pháp thay thế hấp dẫn cho câu chuyện được đặt ra bởi các nhà cung cấp khác nhau trong lĩnh vực này.

Nhiều vấn đề được gán cho SOA thực ra là vấn đề với những thứ như giao thức truyền thông (ví dụ, `SOAP`), `middleware` của nhà cung cấp, thiếu hướng dẫn về mức độ chi tiết của dịch vụ, hoặc hướng dẫn sai về việc chọn nơi để chia tách hệ thống của bạn. Chúng ta sẽ giải quyết từng vấn đề này lần lượt trong phần còn lại của cuốn sách. Một người hoài nghi có thể cho rằng các nhà cung cấp đã đồng lõa (và trong một số trường hợp đã thúc đẩy) phong trào SOA như một cách để bán thêm sản phẩm, và chính những sản phẩm đó cuối cùng đã làm suy yếu mục tiêu của SOA.

Phần lớn kiến thức thông thường xoay quanh SOA không giúp bạn hiểu cách chia một thứ lớn thành một thứ nhỏ. Nó không nói về việc *lớn là quá lớn*. Nó không nói đủ về các cách thực tế trong thế giới thực để đảm bảo rằng các dịch vụ không trở nên quá phụ thuộc (coupled). Số lượng những điều không được nói đến chính là nơi bắt nguồn của nhiều cạm bẫy liên quan đến SOA.

Phương pháp microservice đã nổi lên từ việc sử dụng trong thế giới thực, lấy sự hiểu biết tốt hơn của chúng ta về hệ thống và kiến trúc để thực hiện SOA một cách tốt đẹp. Vì vậy, bạn nên nghĩ về microservices như một cách tiếp cận cụ thể cho SOA, giống như `XP` hoặc `Scrum` là những cách tiếp cận cụ thể cho phát triển phần mềm Agile.

#### **Các kỹ thuật phân tách khác (Other Decompositional Techniques)**

Khi bạn xem xét kỹ hơn, nhiều lợi thế của một kiến trúc dựa trên microservice đến từ bản chất chi tiết của nó và thực tế là nó cung cấp cho bạn nhiều lựa chọn hơn về cách giải quyết vấn đề. Nhưng liệu các kỹ thuật phân tách tương tự có thể đạt được những lợi ích tương tự không?

##### **Thư viện dùng chung (Shared Libraries)**

Một kỹ thuật phân tách rất tiêu chuẩn được tích hợp sẵn trong hầu như mọi ngôn ngữ là chia nhỏ một `codebase` thành nhiều thư viện. Các thư viện này có thể được cung cấp bởi bên thứ ba, hoặc được tạo ra trong chính tổ chức của bạn.

Các thư viện cung cấp cho bạn một cách để chia sẻ chức năng giữa các đội và dịch vụ. Tôi có thể tạo một bộ các tiện ích thu thập hữu ích, ví dụ, hoặc có thể là một thư viện thống kê có thể được tái sử dụng.

Các đội có thể tự tổ chức xung quanh các thư viện này, và chính các thư viện cũng có thể được tái sử dụng. Nhưng có một số nhược điểm.

*   Đầu tiên, bạn mất đi sự đa dạng thực sự về công nghệ (`technology heterogeneity`). Thư viện thường phải được viết bằng cùng một ngôn ngữ, hoặc ít nhất là chạy trên cùng một nền tảng.
*   Thứ hai, khả năng bạn có thể mở rộng quy mô các phần của hệ thống một cách độc lập với nhau bị hạn chế.
*   Tiếp theo, trừ khi bạn đang sử dụng các thư viện liên kết động (`dynamically linked libraries`), bạn không thể triển khai một thư viện mới mà không triển khai lại toàn bộ tiến trình, vì vậy khả năng triển khai các thay đổi một cách cô lập của bạn bị giảm.
*   Và có lẽ điểm mấu chốt là bạn thiếu các đường nối (seams) rõ ràng xung quanh đó để dựng lên các biện pháp an toàn kiến trúc nhằm đảm bảo khả năng phục hồi của hệ thống.

Các thư viện dùng chung có vị trí của chúng. Bạn sẽ thấy mình tạo ra mã cho các tác vụ chung không đặc trưng cho miền nghiệp vụ của bạn mà bạn muốn tái sử dụng trong toàn tổ chức, đó là một ứng cử viên rõ ràng để trở thành một thư viện có thể tái sử dụng. Tuy nhiên, bạn cần phải cẩn thận. Mã dùng chung được sử dụng để giao tiếp giữa các dịch vụ có thể trở thành một điểm ghép nối (`point of coupling`), một điều chúng ta sẽ thảo luận trong **Chương 4**.

Các dịch vụ có thể và nên sử dụng nhiều thư viện của bên thứ ba để tái sử dụng mã chung. Nhưng chúng không đưa chúng ta đi hết con đường.

##### **Module**

Một số ngôn ngữ cung cấp các kỹ thuật phân tách theo `module` của riêng chúng, vượt ra ngoài các thư viện đơn giản. Chúng cho phép một số quản lý vòng đời của các `module`, sao cho chúng có thể được triển khai vào một tiến trình đang chạy, cho phép bạn thực hiện thay đổi mà không cần phải dừng toàn bộ tiến trình.

**Sáng kiến Cổng Nguồn Mở (Open Source Gateway Initiative - OSGI)** đáng được nhắc đến như một phương pháp phân tách theo `module` cụ thể cho công nghệ. Bản thân Java không có một khái niệm thực sự về `module`, và chúng ta sẽ phải đợi ít nhất đến Java 9 để thấy điều này được thêm vào ngôn ngữ. `OSGI`, xuất hiện như một `framework` cho phép cài đặt các `plug-in` vào Eclipse Java IDE, hiện được sử dụng như một cách để trang bị thêm khái niệm `module` trong Java thông qua một thư viện.

Vấn đề với `OSGI` là nó đang cố gắng thực thi những thứ như quản lý vòng đời `module` mà không có đủ sự hỗ trợ trong chính ngôn ngữ đó. Điều này dẫn đến việc các tác giả `module` phải làm nhiều việc hơn để đảm bảo sự cô lập `module` đúng cách. Trong ranh giới của một tiến trình, cũng dễ dàng hơn nhiều để rơi vào bẫy làm cho các `module` quá phụ thuộc vào nhau, gây ra đủ loại vấn đề. Kinh nghiệm của riêng tôi với `OSGI`, được chia sẻ bởi các đồng nghiệp trong ngành, là ngay cả với các đội tốt, `OSGI` cũng dễ dàng trở thành một nguồn phức tạp lớn hơn nhiều so với lợi ích của nó.

`Erlang` theo một cách tiếp cận khác, trong đó các `module` được tích hợp vào `runtime` của ngôn ngữ. Do đó, `Erlang` là một phương pháp rất trưởng thành để phân tách theo `module`. Các `module` Erlang có thể được dừng, khởi động lại và nâng cấp mà không có vấn đề gì. Erlang thậm chí còn hỗ trợ chạy nhiều hơn một phiên bản của `module` tại một thời điểm, cho phép nâng cấp `module` một cách duyên dáng hơn.

Khả năng của các `module` của Erlang thực sự ấn tượng, nhưng ngay cả khi chúng ta đủ may mắn để sử dụng một nền tảng có những khả năng này, chúng ta vẫn có những thiếu sót tương tự như với các thư viện dùng chung thông thường. Chúng ta bị hạn chế nghiêm ngặt trong khả năng sử dụng các công nghệ mới, hạn chế trong cách chúng ta có thể mở rộng quy mô một cách độc lập, có thể trôi dạt về phía các kỹ thuật tích hợp quá phụ thuộc, và thiếu các đường nối cho các biện pháp an toàn kiến trúc.

Có một quan sát cuối cùng đáng chia sẻ. Về mặt kỹ thuật, việc tạo ra các `module` độc lập, được cấu trúc tốt trong một tiến trình `monolithic` duy nhất là hoàn toàn có thể. Tuy nhiên, chúng ta hiếm khi thấy điều này xảy ra. Các `module` nhanh chóng trở nên phụ thuộc chặt chẽ với phần còn lại của mã, từ bỏ một trong những lợi ích chính của chúng. Việc có một sự tách biệt ở ranh giới tiến trình thực sự thực thi vệ sinh sạch sẽ về mặt này (hoặc ít nhất là làm cho việc làm sai trở nên khó khăn hơn!). Tất nhiên, tôi sẽ không đề nghị rằng đây nên là động lực chính cho việc tách tiến trình, nhưng thật thú vị khi những lời hứa về sự tách biệt `module` trong ranh giới tiến trình hiếm khi được thực hiện trong thế giới thực.

Vì vậy, trong khi phân tách theo `module` trong ranh giới của một tiến trình có thể là điều bạn muốn làm cũng như phân tách hệ thống của mình thành các dịch vụ, nhưng bản thân nó sẽ không giúp giải quyết mọi thứ. Nếu bạn là một cửa hàng `Erlang` thuần túy, chất lượng triển khai `module` của Erlang có thể giúp bạn đi một chặng đường rất dài, nhưng tôi nghi ngờ rằng nhiều người trong số các bạn không ở trong tình huống đó. Đối với phần còn lại của chúng ta, chúng ta nên xem các `module` như cung cấp các loại lợi ích tương tự như các thư viện dùng chung.

#### **Không có viên đạn bạc nào cả (No Silver Bullet)**

Trước khi kết thúc, tôi nên chỉ ra rằng microservices không phải là bữa trưa miễn phí hay viên đạn bạc, và là một lựa chọn tồi như một cây búa vàng. Chúng có tất cả những sự phức tạp liên quan của các hệ thống phân tán, và trong khi chúng ta đã học được rất nhiều về cách quản lý các hệ thống phân tán tốt (mà chúng ta sẽ thảo luận trong suốt cuốn sách) thì nó vẫn còn khó. Nếu bạn đến từ quan điểm của một hệ thống `monolithic`, bạn sẽ phải giỏi hơn rất nhiều trong việc xử lý triển khai, kiểm thử và giám sát để mở khóa những lợi ích mà chúng ta đã đề cập cho đến nay. Bạn cũng sẽ cần suy nghĩ khác về cách bạn mở rộng quy mô hệ thống của mình và đảm bảo chúng có khả năng phục hồi. Cũng đừng ngạc nhiên nếu những thứ như giao dịch phân tán (`distributed transactions`) hoặc định lý CAP bắt đầu làm bạn đau đầu!

Mỗi công ty, tổ chức và hệ thống đều khác nhau. Một số yếu tố sẽ ảnh hưởng đến việc liệu microservices có phù hợp với bạn hay không, và mức độ quyết liệt bạn có thể áp dụng chúng. Trong mỗi chương của cuốn sách này, tôi sẽ cố gắng cung cấp cho bạn hướng dẫn nêu bật những cạm bẫy tiềm tàng, điều này sẽ giúp bạn vạch ra một con đường vững chắc.

#### **Tóm tắt**

Hy vọng đến bây giờ bạn đã biết microservice là gì, điều gì làm cho nó khác biệt với các kỹ thuật sáng tác khác, và một số lợi thế chính của nó là gì. Trong mỗi chương tiếp theo, chúng ta sẽ đi sâu hơn vào cách đạt được những lợi ích này và cách tránh một số cạm bẫy phổ biến.

Có một số chủ đề cần đề cập, nhưng chúng ta cần bắt đầu ở đâu đó. Một trong những thách thức chính mà microservices giới thiệu là sự thay đổi trong vai trò của những người thường hướng dẫn sự phát triển của hệ thống của chúng ta: các kiến trúc sư. Tiếp theo, chúng ta sẽ xem xét một số cách tiếp cận khác nhau cho vai trò này để có thể đảm bảo chúng ta nhận được nhiều nhất từ kiến trúc mới này.

---

### **2. Kiến trúc sư Tiến hóa (The Evolutionary Architect)**

Như chúng ta đã thấy cho đến nay, microservices cho chúng ta rất nhiều sự lựa chọn, và theo đó là rất nhiều quyết định cần phải đưa ra. Ví dụ, chúng ta nên sử dụng bao nhiêu công nghệ khác nhau, chúng ta có nên để các đội khác nhau sử dụng các thành ngữ lập trình (programming idioms) khác nhau, và chúng ta có nên chia tách hay hợp nhất một dịch vụ? Chúng ta làm thế nào để đưa ra những quyết định này? Với tốc độ thay đổi nhanh hơn và môi trường linh hoạt hơn mà các kiến trúc này cho phép, vai trò của kiến trúc sư cũng phải thay đổi. Trong chương này, tôi sẽ đưa ra một quan điểm khá thiên vị về vai trò của một kiến trúc sư, và hy vọng sẽ tung ra một đòn tấn công cuối cùng vào tháp ngà.

#### **Những so sánh không chính xác (Inaccurate Comparisons)**

> *You keep using that word. I do not think it means what you think it means.*
>
> —Inigo Montoya, từ *The Princess Bride*

Kiến trúc sư có một công việc quan trọng. Họ chịu trách nhiệm đảm bảo chúng ta có một tầm nhìn kỹ thuật thống nhất, một tầm nhìn sẽ giúp chúng ta cung cấp hệ thống mà khách hàng cần. Ở một số nơi, họ có thể chỉ phải làm việc với một đội, trong trường hợp đó vai trò của kiến trúc sư và trưởng nhóm kỹ thuật (technical lead) thường là một. Ở những nơi khác, họ có thể đang định hình tầm nhìn cho cả một chương trình công việc, phối hợp với nhiều đội trên khắp thế giới, hoặc thậm chí là cả một tổ chức. Dù họ hoạt động ở cấp độ nào, đó là một vai trò khó xác định, và mặc dù nó thường là con đường thăng tiến sự nghiệp rõ ràng cho các lập trình viên trong các tổ chức doanh nghiệp, nó cũng là một vai trò nhận được nhiều chỉ trích hơn hầu hết các vai trò khác. Hơn bất kỳ vai trò nào khác, kiến trúc sư có thể có tác động trực tiếp đến chất lượng của các hệ thống được xây dựng, đến điều kiện làm việc của các đồng nghiệp, và đến khả năng phản ứng với sự thay đổi của tổ chức, vậy mà chúng ta lại thường xuyên làm sai vai trò này. Tại sao lại như vậy?

Ngành của chúng ta còn non trẻ. Đây là điều chúng ta dường như quên mất, nhưng chúng ta chỉ mới tạo ra các chương trình chạy trên những gì chúng ta nhận ra là máy tính trong khoảng 70 năm. Do đó, chúng ta liên tục tìm đến các ngành nghề khác để cố gắng giải thích những gì chúng ta làm. Chúng ta không phải là bác sĩ y khoa hay kỹ sư, nhưng chúng ta cũng không phải là thợ sửa ống nước hay thợ điện. Thay vào đó, chúng ta rơi vào một vùng đất trung gian nào đó, điều này làm cho xã hội khó hiểu chúng ta, hoặc để chúng ta hiểu được vị trí của mình.

Vì vậy, chúng ta mượn tên từ các ngành nghề khác. Chúng ta tự gọi mình là "kỹ sư" phần mềm, hoặc "kiến trúc sư". Nhưng chúng ta không phải, phải không? Kiến trúc sư và kỹ sư có một sự nghiêm ngặt và kỷ luật mà chúng ta chỉ có thể mơ ước, và tầm quan trọng của họ trong xã hội được hiểu rõ. Tôi nhớ đã nói chuyện với một người bạn của mình, một ngày trước khi anh ấy trở thành một kiến trúc sư có trình độ. "Ngày mai," anh ấy nói, "nếu tôi cho bạn lời khuyên ở quán rượu về cách xây dựng một thứ gì đó và nó sai, tôi sẽ phải chịu trách nhiệm. Tôi có thể bị kiện, vì trong mắt pháp luật, tôi bây giờ là một kiến trúc sư có trình độ và tôi phải chịu trách nhiệm nếu tôi làm sai." Tầm quan trọng của những công việc này đối với xã hội có nghĩa là có những bằng cấp bắt buộc mà mọi người phải đáp ứng. Ví dụ, ở Anh, cần tối thiểu bảy năm học để bạn có thể được gọi là kiến trúc sư. Nhưng những công việc này cũng dựa trên một khối kiến thức có từ hàng ngàn năm. Còn chúng ta thì sao? Không hẳn. Đó cũng là lý do tại sao tôi xem hầu hết các hình thức chứng chỉ CNTT là vô giá trị, vì chúng ta biết quá ít về cái gì là tốt.

Một phần trong chúng ta muốn được công nhận, vì vậy chúng ta mượn tên từ các ngành nghề khác đã có sự công nhận mà ngành chúng ta khao khát. Nhưng điều này có thể gây hại gấp đôi.
*   **Thứ nhất**, nó ngụ ý rằng chúng ta biết mình đang làm gì, trong khi rõ ràng là không. Tôi sẽ không nói rằng các tòa nhà và cây cầu không bao giờ sụp đổ, nhưng chúng sụp đổ ít hơn nhiều so với số lần chương trình của chúng ta sẽ bị treo, làm cho việc so sánh với các kỹ sư trở nên khá không công bằng.
*   **Thứ hai**, các phép loại suy bị phá vỡ rất nhanh khi được xem xét kỹ lưỡng. Đảo ngược lại, nếu việc xây cầu giống như lập trình, thì giữa chừng chúng ta sẽ phát hiện ra rằng bờ bên kia giờ đã cách xa hơn 50 mét, rằng nó thực sự là bùn thay vì đá granit, và thay vì xây dựng một cây cầu đi bộ, chúng ta lại đang xây dựng một cây cầu đường bộ. Phần mềm của chúng ta không bị ràng buộc bởi các quy tắc vật lý giống như các kiến trúc sư hoặc kỹ sư thực sự phải đối mặt, và những gì chúng ta tạo ra được thiết kế để linh hoạt, thích ứng và phát triển theo yêu cầu của người dùng.

Có lẽ thuật ngữ *kiến trúc sư* đã gây ra nhiều tác hại nhất. Ý tưởng về một người vẽ ra các kế hoạch chi tiết cho người khác giải thích, và mong đợi điều này được thực hiện. Sự cân bằng của một phần nghệ sĩ, một phần kỹ sư, giám sát việc tạo ra một tầm nhìn thường là duy nhất, với tất cả các quan điểm khác là phụ thuộc, ngoại trừ sự phản đối thỉnh thoảng từ kỹ sư kết cấu về các định luật vật lý. Trong ngành của chúng ta, quan điểm này về kiến trúc sư dẫn đến một số thực tiễn tồi tệ. Sơ đồ này đến sơ đồ khác, trang này đến trang khác của tài liệu, được tạo ra với mục đích thông báo cho việc xây dựng một hệ thống hoàn hảo, mà không tính đến tương lai không thể biết trước được một cách cơ bản. Hoàn toàn không có bất kỳ sự hiểu biết nào về việc nó sẽ khó thực hiện đến mức nào, hoặc liệu nó có thực sự hoạt động hay không, chưa kể đến bất kỳ khả năng nào để thay đổi khi chúng ta học hỏi thêm.

Khi chúng ta so sánh mình với các kỹ sư hoặc kiến trúc sư, chúng ta đang có nguy cơ làm hại tất cả mọi người. Thật không may, chúng ta bị mắc kẹt với từ *kiến trúc sư* bây giờ. Vì vậy, điều tốt nhất chúng ta có thể làm là định nghĩa lại ý nghĩa của nó trong bối cảnh của chúng ta.

---
### **Tầm nhìn Tiến hóa cho Kiến trúc sư (An Evolutionary Vision for the Architect)**

Yêu cầu của chúng ta thay đổi nhanh hơn nhiều so với những người thiết kế và xây dựng các tòa nhà—cũng như các công cụ và kỹ thuật mà chúng ta có. Những thứ chúng ta tạo ra không phải là những điểm cố định trong thời gian. Một khi được đưa vào sản xuất, phần mềm của chúng ta sẽ tiếp tục phát triển khi cách nó được sử dụng thay đổi. Đối với hầu hết những thứ chúng ta tạo ra, chúng ta phải chấp nhận rằng một khi phần mềm đến tay khách hàng, chúng ta sẽ phải phản ứng và thích ứng, thay vì nó là một tạo tác không bao giờ thay đổi. Do đó, các kiến trúc sư của chúng ta cần chuyển suy nghĩ của họ từ việc tạo ra sản phẩm cuối cùng hoàn hảo, và thay vào đó tập trung vào việc giúp tạo ra một khuôn khổ trong đó các hệ thống phù hợp có thể xuất hiện, và tiếp tục phát triển khi chúng ta học hỏi thêm.

Mặc dù tôi đã dành phần lớn chương này để cảnh báo bạn không nên so sánh mình quá nhiều với các ngành nghề khác, có một phép loại suy mà tôi thích khi nói đến vai trò của kiến trúc sư CNTT và tôi nghĩ rằng nó thể hiện tốt hơn những gì chúng ta muốn vai trò này trở thành. Erik Doernenburg lần đầu tiên chia sẻ với tôi ý tưởng rằng chúng ta nên nghĩ về vai trò của mình giống như các nhà quy hoạch đô thị hơn là các kiến trúc sư cho môi trường xây dựng. Vai trò của nhà quy hoạch đô thị nên quen thuộc với bất kỳ ai trong số các bạn đã từng chơi SimCity. Vai trò của một nhà quy hoạch đô thị là xem xét vô số nguồn thông tin, và sau đó cố gắng tối ưu hóa cách bố trí của một thành phố để phù hợp nhất với nhu cầu của người dân ngày nay, có tính đến việc sử dụng trong tương lai. Tuy nhiên, cách anh ta ảnh hưởng đến cách thành phố phát triển là rất thú vị. Anh ta không nói, "xây dựng tòa nhà cụ thể này ở đó"; thay vào đó, anh ta **phân vùng (zones)** một thành phố. Vì vậy, như trong SimCity, bạn có thể chỉ định một phần của thành phố là khu công nghiệp, và một phần khác là khu dân cư. Sau đó, tùy thuộc vào những người khác quyết định chính xác những tòa nhà nào được tạo ra, nhưng có những hạn chế: nếu bạn muốn xây dựng một nhà máy, nó sẽ phải ở trong khu công nghiệp. Thay vì lo lắng quá nhiều về những gì xảy ra trong một khu vực, nhà quy hoạch đô thị sẽ dành nhiều thời gian hơn để tìm ra cách mọi người và các tiện ích di chuyển từ khu vực này sang khu vực khác.

Nhiều hơn một người đã ví một thành phố như một sinh vật sống. Thành phố thay đổi theo thời gian. Nó thay đổi và phát triển khi cư dân của nó sử dụng nó theo những cách khác nhau, hoặc khi các lực lượng bên ngoài định hình nó. Nhà quy hoạch đô thị làm hết sức mình để dự đoán những thay đổi này, nhưng chấp nhận rằng việc cố gắng kiểm soát trực tiếp tất cả các khía cạnh của những gì xảy ra là vô ích.

Sự so sánh với phần mềm nên rõ ràng. Khi người dùng của chúng ta sử dụng phần mềm của chúng ta, chúng ta cần phản ứng và thay đổi. Chúng ta không thể lường trước được mọi thứ sẽ xảy ra, và do đó, thay vì lên kế hoạch cho mọi tình huống, chúng ta nên lên kế hoạch cho phép sự thay đổi bằng cách tránh thôi thúc chỉ định quá mức mọi thứ. Thành phố của chúng ta—hệ thống—cần phải là một nơi tốt đẹp, hạnh phúc cho tất cả mọi người sử dụng nó. Một điều mà mọi người thường quên là hệ thống của chúng ta không chỉ phục vụ người dùng; nó còn phục vụ các lập trình viên và những người vận hành, những người cũng phải làm việc ở đó, và có công việc đảm bảo nó có thể thay đổi khi cần thiết. Mượn một thuật ngữ từ Frank Buschmann, kiến trúc sư có nhiệm vụ đảm bảo rằng hệ thống là **có thể sống được (habitable)** cho các lập trình viên.

Một nhà quy hoạch đô thị, giống như một kiến trúc sư, cũng cần biết khi nào kế hoạch của mình không được tuân theo. Vì anh ta ít ra lệnh hơn, số lần anh ta cần can thiệp để sửa chữa hướng đi nên là tối thiểu, nhưng nếu ai đó quyết định xây dựng một nhà máy xử lý nước thải trong khu dân cư, anh ta cần có khả năng đóng cửa nó.

Vì vậy, các kiến trúc sư của chúng ta với tư cách là các nhà quy hoạch đô thị cần phải định hướng theo những nét vẽ rộng, và chỉ tham gia vào việc chỉ định rất cụ thể về chi tiết triển khai trong các trường hợp hạn chế. Họ cần đảm bảo rằng hệ thống phù hợp với mục đích hiện tại, nhưng cũng là một nền tảng cho tương lai. Và họ cần đảm bảo rằng đó là một hệ thống làm cho người dùng và lập trình viên đều hạnh phúc. Điều này nghe có vẻ như một nhiệm vụ khá cao cả. Chúng ta bắt đầu từ đâu?

#### **Phân vùng (Zoning)**

Để tiếp tục phép ẩn dụ về kiến trúc sư như một nhà quy hoạch đô thị trong giây lát, vậy các "khu vực" (`zones`) của chúng ta là gì? Đây là ranh giới dịch vụ của chúng ta, hoặc có thể là các nhóm dịch vụ được phân loại một cách thô. Với tư cách là kiến trúc sư, chúng ta cần ít lo lắng hơn về những gì xảy ra *bên trong* khu vực mà tập trung vào những gì xảy ra *giữa* các khu vực. Điều đó có nghĩa là chúng ta cần dành thời gian suy nghĩ về cách các dịch vụ của chúng ta nói chuyện với nhau, hoặc đảm bảo rằng chúng ta có thể giám sát đúng cách sức khỏe tổng thể của hệ thống. Mức độ chúng ta tham gia vào bên trong khu vực sẽ thay đổi đôi chút. Nhiều tổ chức đã áp dụng microservices để tối đa hóa quyền tự chủ của các đội, điều mà chúng ta sẽ mở rộng trong **Chương 10**. Nếu bạn ở trong một tổ chức như vậy, bạn sẽ dựa nhiều hơn vào đội để đưa ra quyết định cục bộ đúng đắn.

Nhưng giữa các khu vực, hoặc các ô vuông trên sơ đồ kiến trúc truyền thống của chúng ta, chúng ta cần phải cẩn thận; làm sai ở đây dẫn đến đủ loại vấn đề và có thể rất khó sửa chữa.

Trong mỗi dịch vụ, bạn có thể chấp nhận việc đội sở hữu khu vực đó chọn một `technology stack` hoặc `data store` khác nhau. Tất nhiên, các mối quan tâm khác có thể xuất hiện ở đây. Xu hướng của bạn để cho các đội chọn công cụ phù hợp cho công việc có thể bị giảm bớt bởi thực tế là việc tuyển dụng người hoặc di chuyển họ giữa các đội trở nên khó khăn hơn nếu bạn có 10 `technology stacks` khác nhau để hỗ trợ. Tương tự, nếu mỗi đội chọn một `data store` hoàn toàn khác nhau, bạn có thể thấy mình thiếu đủ kinh nghiệm để chạy bất kỳ cái nào trong số chúng ở quy mô lớn. Ví dụ, Netflix đã chủ yếu chuẩn hóa trên Cassandra như một công nghệ `data-store`. Mặc dù nó có thể không phải là phù hợp nhất cho tất cả các trường hợp của họ, Netflix cảm thấy rằng giá trị thu được từ việc xây dựng công cụ và chuyên môn xung quanh Cassandra quan trọng hơn là phải hỗ trợ và vận hành ở quy mô lớn nhiều nền tảng khác có thể phù hợp hơn cho một số tác vụ nhất định. Netflix là một ví dụ cực đoan, nơi quy mô có thể là yếu tố chi phối mạnh nhất, nhưng bạn đã hiểu ý.

Giữa các dịch vụ là nơi mọi thứ có thể trở nên lộn xộn. Nếu một dịch vụ quyết định phơi bày `REST` qua `HTTP`, một dịch vụ khác sử dụng `protocol buffers`, và một dịch vụ thứ ba sử dụng Java `RMI`, thì việc tích hợp có thể trở thành một cơn ác mộng vì các dịch vụ tiêu thụ phải hiểu và hỗ trợ nhiều kiểu trao đổi. Đây là lý do tại sao tôi cố gắng tuân thủ hướng dẫn rằng chúng ta nên "lo lắng về những gì xảy ra giữa các ô vuông, và tự do về những gì xảy ra bên trong."

> #### **Kiến trúc sư viết mã (The Coding Architect)**
>
> Nếu chúng ta muốn đảm bảo rằng các hệ thống chúng ta tạo ra là "có thể sống được" (`habitable`) cho các lập trình viên của mình, thì các kiến trúc sư của chúng ta cần hiểu được tác động của các quyết định của họ. Ít nhất, điều này có nghĩa là dành thời gian với đội, và lý tưởng nhất là nó nên có nghĩa là những nhà phát triển này thực sự dành thời gian viết mã cùng với đội. Đối với những bạn thực hành lập trình cặp (`pair programming`), việc một kiến trúc sư tham gia vào một đội trong một khoảng thời gian ngắn với tư cách là một thành viên của cặp trở thành một vấn đề đơn giản. Lý tưởng nhất, bạn nên làm việc trên các `stories` bình thường, để thực sự hiểu công việc bình thường là như thế nào. Tôi không thể nhấn mạnh đủ tầm quan trọng của việc kiến trúc sư thực sự ngồi cùng với đội! Điều này hiệu quả hơn đáng kể so với việc có một cuộc gọi hoặc chỉ nhìn vào mã của cô ấy.
>
> Về tần suất bạn nên làm điều này, điều đó phụ thuộc rất nhiều vào quy mô của (các) đội bạn đang làm việc cùng. Nhưng điều quan trọng là nó phải là một hoạt động thường xuyên. Ví dụ, nếu bạn đang làm việc với bốn đội, việc dành nửa ngày với mỗi đội mỗi bốn tuần sẽ đảm bảo bạn xây dựng được nhận thức và cải thiện giao tiếp với các đội bạn đang làm việc cùng.

---
#### **Cách tiếp cận dựa trên nguyên tắc (A Principled Approach)**

> *Rules are for the obedience of fools and the guidance of wise men.*
>
> —Thường được cho là của Douglas Bader

Việc đưa ra quyết định trong thiết kế hệ thống là tất cả về sự đánh đổi, và kiến trúc microservice cho chúng ta rất nhiều sự đánh đổi để thực hiện! Khi chọn một `datastore`, chúng ta có chọn một nền tảng mà chúng ta có ít kinh nghiệm hơn, nhưng lại cho chúng ta khả năng mở rộng tốt hơn không? Có ổn không nếu chúng ta có hai `technology stacks` khác nhau trong hệ thống của mình? Còn ba thì sao? Một số quyết định có thể được đưa ra hoàn toàn tại chỗ với thông tin có sẵn cho chúng ta, và đây là những quyết định dễ đưa ra nhất. Nhưng còn những quyết định có thể phải được đưa ra dựa trên thông tin không đầy đủ thì sao?

Việc định hình ở đây có thể giúp ích, và một cách tuyệt vời để giúp định hình việc ra quyết định của chúng ta là xác định một bộ các **nguyên tắc (principles)** và **thực tiễn (practices)** hướng dẫn nó, dựa trên các **mục tiêu (goals)** mà chúng ta đang cố gắng đạt được. Hãy xem xét từng cái một.

##### **Mục tiêu chiến lược (Strategic Goals)**

Vai trò của kiến trúc sư đã đủ khó khăn, vì vậy may mắn là chúng ta thường không phải xác định các mục tiêu chiến lược nữa! Các mục tiêu chiến lược nên nói về nơi công ty của bạn đang hướng tới, và cách nó tự thấy mình làm cho khách hàng hài lòng nhất. Đây sẽ là những mục tiêu cấp cao, và có thể không bao gồm công nghệ gì cả. Chúng có thể được định nghĩa ở cấp công ty hoặc cấp bộ phận. Chúng có thể là những thứ như "Mở rộng sang Đông Nam Á để mở khóa các thị trường mới," hoặc "Để khách hàng đạt được càng nhiều càng tốt bằng cách sử dụng dịch vụ tự phục vụ." Điểm mấu chốt là đây là hướng đi của tổ chức bạn, vì vậy bạn cần đảm bảo công nghệ được điều chỉnh cho phù hợp.

Nếu bạn là người định hình tầm nhìn kỹ thuật của công ty, điều này có thể có nghĩa là bạn sẽ cần dành nhiều thời gian hơn với các bộ phận phi kỹ thuật của tổ chức (hoặc *bên kinh doanh*, như họ thường được gọi). Tầm nhìn thúc đẩy cho doanh nghiệp là gì? Và nó thay đổi như thế nào?

##### **Nguyên tắc (Principles)**

Nguyên tắc là các quy tắc bạn đã đặt ra để điều chỉnh những gì bạn đang làm với một mục tiêu lớn hơn, và đôi khi sẽ thay đổi. Ví dụ, nếu một trong những mục tiêu chiến lược của bạn với tư cách là một tổ chức là giảm thời gian đưa các tính năng mới ra thị trường, bạn có thể xác định một nguyên tắc nói rằng các đội giao hàng có toàn quyền kiểm soát vòng đời của phần mềm của họ để phát hành bất cứ khi nào họ sẵn sàng, độc lập với bất kỳ đội nào khác. Nếu một mục tiêu khác là tổ chức của bạn đang tích cực phát triển sản phẩm của mình ở các quốc gia khác, bạn có thể quyết định thực hiện một nguyên tắc rằng toàn bộ hệ thống phải có thể di động để cho phép nó được triển khai cục bộ nhằm tôn trọng chủ quyền của dữ liệu.

Bạn có lẽ không muốn có quá nhiều nguyên tắc. Ít hơn 10 là một con số tốt—đủ nhỏ để mọi người có thể nhớ chúng, hoặc để vừa trên các áp phích nhỏ. Càng có nhiều nguyên tắc, khả năng chúng chồng chéo hoặc mâu thuẫn với nhau càng lớn.

**12 Yếu tố (12 Factors)** của Heroku là một bộ các nguyên tắc thiết kế được cấu trúc xung quanh mục tiêu giúp bạn tạo ra các ứng dụng hoạt động tốt trên nền tảng Heroku. Chúng cũng có thể có ý nghĩa trong các bối cảnh khác. Một số nguyên tắc thực sự là các ràng buộc dựa trên các hành vi mà ứng dụng của bạn cần thể hiện để hoạt động trên Heroku. Ràng buộc thực sự là một cái gì đó rất khó (hoặc hầu như không thể) thay đổi, trong khi nguyên tắc là những thứ chúng ta quyết định chọn. Bạn có thể quyết định gọi rõ ràng những thứ là nguyên tắc so với những thứ là ràng buộc, để giúp chỉ ra những thứ bạn thực sự không thể thay đổi. Cá nhân tôi nghĩ rằng có thể có một số giá trị trong việc giữ chúng trong cùng một danh sách để khuyến khích việc thách thức các ràng buộc thỉnh thoảng và xem liệu chúng có thực sự bất di bất dịch không!

##### **Thực tiễn (Practices)**

Thực tiễn của chúng ta là cách chúng ta đảm bảo các nguyên tắc của mình được thực hiện. Chúng là một bộ hướng dẫn chi tiết, thực tế để thực hiện các nhiệm vụ. Chúng thường sẽ cụ thể về công nghệ, và nên ở mức đủ thấp để bất kỳ nhà phát triển nào cũng có thể hiểu được. Thực tiễn có thể bao gồm các hướng dẫn về mã hóa, thực tế là tất cả dữ liệu nhật ký cần được thu thập tập trung, hoặc `HTTP/REST` là kiểu tích hợp tiêu chuẩn. Do bản chất kỹ thuật của chúng, các thực tiễn thường sẽ thay đổi thường xuyên hơn các nguyên tắc.

Cũng như các nguyên tắc, đôi khi các thực tiễn phản ánh các ràng buộc trong tổ chức của bạn. Ví dụ, nếu bạn chỉ hỗ trợ CentOS, điều này sẽ cần được phản ánh trong các thực tiễn của bạn.

Các thực tiễn nên củng cố các nguyên tắc của chúng ta. Một nguyên tắc nói rằng các đội giao hàng kiểm soát toàn bộ vòng đời của hệ thống của họ có thể có nghĩa là bạn có một thực tiễn nói rằng tất cả các dịch vụ được triển khai vào các tài khoản AWS biệt lập, cung cấp quản lý tự phục vụ các tài nguyên và sự cô lập khỏi các đội khác.

##### **Kết hợp Nguyên tắc và Thực tiễn (Combining Principles and Practices)**

Nguyên tắc của người này là thực tiễn của người khác. Ví dụ, bạn có thể quyết định gọi việc sử dụng `HTTP/REST` là một nguyên tắc thay vì một thực tiễn. Và điều đó không sao cả. Điểm mấu chốt là có giá trị trong việc có những ý tưởng bao quát hướng dẫn cách hệ thống phát triển, và có đủ chi tiết để mọi người biết cách thực hiện những ý tưởng đó. Đối với một nhóm đủ nhỏ, có lẽ là một đội duy nhất, việc kết hợp các nguyên tắc và thực tiễn có thể ổn. Tuy nhiên, đối với các tổ chức lớn hơn, nơi công nghệ và thực hành làm việc có thể khác nhau, bạn có thể muốn có một bộ thực tiễn khác nhau ở những nơi khác nhau, miễn là chúng đều ánh xạ đến một bộ nguyên tắc chung. Ví dụ, một đội .NET có thể có một bộ thực tiễn, và một đội Java có một bộ khác, với một bộ thực tiễn chung cho cả hai. Tuy nhiên, các nguyên tắc có thể giống nhau cho cả hai.

##### **Một ví dụ thực tế (A Real-World Example)**

Đồng nghiệp của tôi, Evan Bottcher, đã phát triển sơ đồ được hiển thị trong Hình 2-1 trong quá trình làm việc với một trong những khách hàng của chúng tôi. Hình này cho thấy sự tương tác của các mục tiêu, nguyên tắc và thực tiễn ở một định dạng rất rõ ràng. Trong vài năm, các thực tiễn ở phía bên phải sẽ thay đổi khá thường xuyên, trong khi các nguyên tắc vẫn khá tĩnh. Một sơ đồ như thế này có thể được in đẹp trên một tờ giấy và được chia sẻ, và mỗi ý tưởng đủ đơn giản để một nhà phát triển trung bình có thể nhớ. Tất nhiên, có nhiều chi tiết hơn đằng sau mỗi điểm ở đây, nhưng việc có thể trình bày điều này ở dạng tóm tắt là rất hữu ích.

![alt text](<images/Screenshot from 2025-09-29 06-48-22.png>)

**Hình 2-1.** *Một ví dụ thực tế về các nguyên tắc và thực tiễn*

Việc có tài liệu hỗ trợ một số mục này là hợp lý. Tuy nhiên, chủ yếu, tôi thích ý tưởng có mã ví dụ mà bạn có thể xem, kiểm tra và chạy, mà thể hiện những ý tưởng này. Thậm chí tốt hơn, chúng ta có thể tạo ra các công cụ làm đúng ngay từ đầu. Chúng ta sẽ thảo luận sâu hơn về điều đó trong giây lát.

---
#### **Tiêu chuẩn Bắt buộc (The Required Standard)**

Khi bạn đang làm việc thông qua các thực tiễn của mình và suy nghĩ về những sự đánh đổi bạn cần thực hiện, một trong những sự cân bằng cốt lõi cần tìm là mức độ biến đổi cho phép trong hệ thống của bạn. Một trong những cách chính để xác định những gì nên không đổi từ dịch vụ này sang dịch vụ khác là định nghĩa một dịch vụ tốt, hoạt động tốt trông như thế nào. Một dịch vụ "công dân tốt" trong hệ thống của bạn là gì? Nó cần có những khả năng gì để đảm bảo rằng hệ thống của bạn có thể quản lý được và một dịch vụ tồi không làm sụp đổ toàn bộ hệ thống? Và, cũng như với con người, một công dân tốt trong một bối cảnh không phản ánh những gì nó trông như ở một nơi khác. Tuy nhiên, có một số đặc điểm chung của các dịch vụ hoạt động tốt mà tôi nghĩ là khá quan trọng để tuân thủ. Đây là một vài lĩnh vực chính mà việc cho phép quá nhiều sự khác biệt có thể dẫn đến một thời gian khá tồi tệ. Như Ben Christensen từ Netflix đã nói, khi chúng ta nghĩ về bức tranh lớn hơn, "nó cần phải là một hệ thống gắn kết được tạo thành từ nhiều phần nhỏ với vòng đời tự chủ nhưng tất cả đều kết hợp với nhau." Vì vậy, chúng ta cần tìm sự cân bằng giữa việc tối ưu hóa cho quyền tự chủ của từng microservice cá nhân mà không đánh mất tầm nhìn về bức tranh lớn hơn. Việc xác định các thuộc tính rõ ràng mà mỗi dịch vụ nên có là một cách để làm rõ nơi sự cân bằng đó nằm.

##### **Giám sát (Monitoring)**

Điều cần thiết là chúng ta có thể vẽ ra các cái nhìn mạch lạc, xuyên suốt các dịch vụ về sức khỏe hệ thống của chúng ta. Đây phải là một cái nhìn toàn hệ thống, không phải là một cái nhìn cụ thể cho từng dịch vụ. Như chúng ta sẽ thảo luận trong **Chương 8**, việc biết sức khỏe của một dịch vụ cá nhân là hữu ích, nhưng thường chỉ khi bạn đang cố gắng chẩn đoán một vấn đề rộng hơn hoặc hiểu một xu hướng lớn hơn. Để làm điều này dễ dàng nhất có thể, tôi sẽ đề nghị đảm bảo rằng tất cả các dịch vụ đều phát ra các chỉ số sức khỏe và giám sát chung theo cùng một cách.

Bạn có thể chọn áp dụng một cơ chế đẩy, nơi mỗi dịch vụ cần đẩy dữ liệu này vào một vị trí trung tâm. Đối với các chỉ số của bạn, đây có thể là Graphite, và đối với sức khỏe của bạn, đây có thể là Nagios. Hoặc bạn có thể quyết định sử dụng các hệ thống thăm dò (`polling`) để lấy dữ liệu từ chính các nút. Nhưng bất kể bạn chọn gì, hãy cố gắng giữ nó được chuẩn hóa. Hãy làm cho công nghệ bên trong hộp trở nên mờ đục, và không yêu cầu các hệ thống giám sát của bạn phải thay đổi để hỗ trợ nó. Ghi nhật ký cũng thuộc cùng một loại ở đây: chúng ta cần nó ở một nơi.

##### **Giao diện (Interfaces)**

Việc chọn một số lượng nhỏ các công nghệ giao diện được xác định giúp tích hợp các người tiêu dùng mới. Có một tiêu chuẩn là một con số tốt. Hai cũng không quá tệ. Có 20 kiểu tích hợp khác nhau là tồi tệ. Điều này không chỉ là về việc chọn công nghệ và giao thức. Ví dụ, nếu bạn chọn `HTTP/REST`, bạn sẽ sử dụng động từ hay danh từ? Bạn sẽ xử lý việc phân trang tài nguyên như thế nào? Bạn sẽ xử lý việc phiên bản hóa các điểm cuối như thế nào?

##### **An toàn Kiến trúc (Architectural Safety)**

Chúng ta không thể để một dịch vụ hoạt động tồi phá hỏng bữa tiệc của mọi người. Chúng ta phải đảm bảo rằng các dịch vụ của chúng ta tự bảo vệ mình một cách tương ứng khỏi các cuộc gọi không lành mạnh, ở hạ nguồn. Càng có nhiều dịch vụ không xử lý đúng cách sự cố tiềm tàng của các cuộc gọi ở hạ nguồn, hệ thống của chúng ta sẽ càng mong manh hơn. Điều này có nghĩa là bạn có thể sẽ muốn bắt buộc tối thiểu mỗi dịch vụ ở hạ nguồn có `connection pool` riêng của mình, và bạn thậm chí có thể đi xa đến mức nói rằng mỗi dịch vụ cũng sử dụng một **bộ ngắt mạch (circuit breaker)**. Điều này sẽ được đề cập sâu hơn khi chúng ta thảo luận về microservices ở quy mô lớn trong **Chương 11**.

Việc tuân thủ các quy tắc cũng rất quan trọng khi nói đến các mã phản hồi. Nếu các bộ ngắt mạch của bạn dựa vào các mã `HTTP`, và một dịch vụ quyết định gửi lại các mã 2XX cho các lỗi, hoặc nhầm lẫn các mã 4XX với các mã 5XX, thì các biện pháp an toàn này có thể sụp đổ. Các mối quan tâm tương tự cũng sẽ áp dụng ngay cả khi bạn không sử dụng `HTTP`; việc biết sự khác biệt giữa một yêu cầu OK và được xử lý chính xác, một yêu cầu tồi và do đó đã ngăn dịch vụ làm bất cứ điều gì với nó, và một yêu cầu có thể OK nhưng chúng ta không thể biết vì máy chủ đã tắt là chìa khóa để đảm bảo chúng ta có thể thất bại nhanh chóng và theo dõi các vấn đề. Nếu các dịch vụ của chúng ta chơi nhanh và lỏng lẻo với những quy tắc này, chúng ta sẽ kết thúc với một hệ thống dễ bị tổn thương hơn.

#### **Quản trị thông qua Mã nguồn (Governance Through Code)**

Việc ngồi lại với nhau và thống nhất về cách mọi thứ có thể được thực hiện là một ý tưởng tốt. Nhưng việc dành thời gian để đảm bảo mọi người đang tuân theo những hướng dẫn này lại ít thú vị hơn, cũng như việc đặt gánh nặng lên các nhà phát triển để thực hiện tất cả những điều tiêu chuẩn mà bạn mong đợi mỗi dịch vụ phải làm. Tôi là một người rất tin vào việc làm cho việc làm đúng trở nên dễ dàng. Hai kỹ thuật mà tôi đã thấy hoạt động tốt ở đây là sử dụng các **mẫu hình (exemplars)** và cung cấp các **mẫu dịch vụ (service templates)**.

##### **Mẫu hình (Exemplars)**

Tài liệu viết là tốt và hữu ích. Tôi rõ ràng thấy giá trị của nó; sau cùng, tôi đã viết cuốn sách này. Nhưng các nhà phát triển cũng thích mã nguồn, và mã nguồn họ có thể chạy và khám phá. Nếu bạn có một bộ tiêu chuẩn hoặc các thực tiễn tốt nhất mà bạn muốn khuyến khích, thì việc có các mẫu hình mà bạn có thể chỉ cho mọi người là hữu ích. Ý tưởng là mọi người không thể đi sai đường chỉ bằng cách bắt chước một số phần tốt hơn của hệ thống của bạn.

Lý tưởng nhất, đây nên là các dịch vụ thực tế mà bạn có đang hoạt động tốt, thay vì các dịch vụ biệt lập chỉ được triển khai để trở thành những ví dụ hoàn hảo. Bằng cách đảm bảo các mẫu hình của bạn thực sự đang được sử dụng, bạn đảm bảo rằng tất cả các nguyên tắc bạn có thực sự có ý nghĩa.

##### **Mẫu dịch vụ được Tùy chỉnh (Tailored Service Template)**

Sẽ không tuyệt vời sao nếu bạn có thể làm cho tất cả các nhà phát triển tuân theo hầu hết các hướng dẫn bạn có với rất ít công việc? Điều gì sẽ xảy ra nếu, ngay từ đầu, các nhà phát triển đã có sẵn hầu hết mã nguồn để thực hiện các thuộc tính cốt lõi mà mỗi dịch vụ cần?

**Dropwizard** và **Karyon** là hai `microcontainer` dựa trên JVM, mã nguồn mở. Chúng hoạt động theo những cách tương tự, tập hợp một bộ thư viện để cung cấp các tính năng như kiểm tra sức khỏe, phục vụ `HTTP`, hoặc phơi bày các chỉ số. Vì vậy, ngay từ đầu, bạn có một dịch vụ hoàn chỉnh với một `servlet container` nhúng có thể được khởi chạy từ dòng lệnh. Đây là một cách tuyệt vời để bắt đầu, nhưng tại sao lại dừng lại ở đó? Trong khi bạn đang làm điều đó, tại sao không lấy một cái gì đó như Dropwizard hoặc Karyon, và thêm nhiều tính năng hơn để nó trở nên tương thích với bối cảnh của bạn?

Ví dụ, bạn có thể muốn bắt buộc sử dụng các bộ ngắt mạch. Trong trường hợp đó, bạn có thể tích hợp một thư viện bộ ngắt mạch như **Hystrix**. Hoặc bạn có thể có một thực tiễn rằng tất cả các chỉ số của bạn cần được gửi đến một máy chủ Graphite trung tâm, vì vậy có lẽ hãy kéo vào một thư viện mã nguồn mở như **Metrics** của Dropwizard và cấu hình nó để, ngay từ đầu, thời gian phản hồi và tỷ lệ lỗi được đẩy tự động đến một vị trí đã biết.

Bằng cách tùy chỉnh một mẫu dịch vụ như vậy cho bộ thực tiễn phát triển của riêng bạn, bạn đảm bảo rằng các đội có thể bắt đầu nhanh hơn, và cũng đảm bảo rằng các nhà phát triển phải đi đường vòng để làm cho dịch vụ của họ hoạt động tồi.

Tất nhiên, nếu bạn áp dụng nhiều `technology stacks` khác nhau, bạn sẽ cần một mẫu dịch vụ phù hợp cho mỗi cái. Mặc dù vậy, đây có thể là một cách bạn tinh tế hạn chế các lựa chọn ngôn ngữ trong các đội của mình. Nếu mẫu dịch vụ nội bộ chỉ hỗ trợ Java, thì mọi người có thể bị nản lòng khi chọn các `stacks` thay thế nếu họ phải tự làm nhiều việc hơn. Ví dụ, Netflix đặc biệt quan tâm đến các khía cạnh như khả năng chịu lỗi, để đảm bảo rằng sự cố của một phần hệ thống của họ không thể làm sụp đổ mọi thứ. Để xử lý điều này, một lượng lớn công việc đã được thực hiện để đảm bảo rằng có các thư viện máy khách trên JVM để cung cấp cho các đội các công cụ họ cần để giữ cho dịch vụ của họ hoạt động tốt. Bất kỳ ai giới thiệu một `technology stack` mới sẽ có nghĩa là phải tái tạo lại tất cả nỗ lực này. Mối quan tâm chính của Netflix ít hơn về nỗ lực trùng lặp, và nhiều hơn về thực tế là rất dễ làm sai điều này. Nguy cơ một dịch vụ bị triển khai sai khả năng chịu lỗi mới là cao nếu nó có thể tác động đến nhiều hơn hệ thống. Netflix giảm thiểu điều này bằng cách sử dụng các dịch vụ `sidecar`, giao tiếp cục bộ với một JVM đang sử dụng các thư viện phù hợp.

Bạn phải cẩn thận rằng việc tạo ra mẫu dịch vụ không trở thành công việc của một đội công cụ hoặc kiến trúc trung tâm, người ra lệnh cách mọi thứ nên được thực hiện, mặc dù là thông qua mã nguồn. Việc xác định các thực tiễn bạn sử dụng nên là một hoạt động tập thể, vì vậy lý tưởng nhất là (các) đội của bạn nên cùng chịu trách nhiệm cập nhật mẫu này (một cách tiếp cận mã nguồn mở nội bộ hoạt động tốt ở đây).

Tôi cũng đã thấy tinh thần và năng suất của nhiều đội bị phá hủy bởi việc bị áp đặt một `framework` bắt buộc. Trong một nỗ lực cải thiện việc tái sử dụng mã, ngày càng nhiều công việc được đặt vào một `framework` tập trung cho đến khi nó trở thành một con quái vật áp đảo. Nếu bạn quyết định sử dụng một mẫu dịch vụ được tùy chỉnh, hãy suy nghĩ rất cẩn thận về công việc của nó. Lý tưởng nhất, việc sử dụng nó nên hoàn toàn là tùy chọn, nhưng nếu bạn sẽ mạnh mẽ hơn trong việc áp dụng nó, bạn cần hiểu rằng sự dễ sử dụng cho các nhà phát triển phải là một lực lượng hướng dẫn chính.

Cũng cần nhận thức được những nguy cơ của mã dùng chung. Trong mong muốn tạo ra mã có thể tái sử dụng, chúng ta có thể giới thiệu các nguồn ghép nối giữa các dịch vụ. Ít nhất một tổ chức mà tôi đã nói chuyện lo lắng về điều này đến mức họ thực sự sao chép mã mẫu dịch vụ của mình thủ công vào mỗi dịch vụ. Điều này có nghĩa là một bản nâng cấp cho mẫu dịch vụ cốt lõi mất nhiều thời gian hơn để được áp dụng trên toàn hệ thống của họ, nhưng điều này ít đáng lo ngại hơn đối với họ so với nguy cơ ghép nối. Các đội khác mà tôi đã nói chuyện chỉ đơn giản coi mẫu dịch vụ là một phụ thuộc nhị phân dùng chung, mặc dù họ phải rất siêng năng trong việc không để xu hướng DRY (đừng lặp lại chính mình) dẫn đến một hệ thống quá phụ thuộc! Đây là một chủ đề tinh tế, vì vậy chúng ta sẽ khám phá nó chi tiết hơn trong **Chương 4**.

---
#### **Nợ Kỹ thuật (Technical Debt)**

Chúng ta thường ở trong những tình huống mà chúng ta không thể tuân thủ hoàn toàn tầm nhìn kỹ thuật của mình. Thường thì, chúng ta cần phải đưa ra lựa chọn cắt giảm một vài góc để đưa ra một số tính năng khẩn cấp. Đây chỉ là một sự đánh đổi nữa mà chúng ta sẽ thấy mình phải thực hiện. Tầm nhìn kỹ thuật của chúng ta tồn tại có lý do. Nếu chúng ta đi chệch khỏi lý do này, nó có thể có lợi ích ngắn hạn nhưng lại có chi phí dài hạn. Một khái niệm giúp chúng ta hiểu sự đánh đổi này là **nợ kỹ thuật (technical debt)**. Khi chúng ta tích lũy nợ kỹ thuật, giống như nợ trong thế giới thực, nó có một chi phí liên tục, và là thứ chúng ta muốn trả hết.

Đôi khi nợ kỹ thuật không chỉ là thứ chúng ta gây ra bằng cách đi đường tắt. Điều gì xảy ra nếu tầm nhìn của chúng ta cho hệ thống thay đổi, nhưng không phải tất cả hệ thống của chúng ta đều phù hợp? Trong tình huống này, chúng ta cũng đã tạo ra các nguồn nợ kỹ thuật mới.

Công việc của kiến trúc sư là nhìn vào bức tranh lớn hơn, và hiểu sự cân bằng này. Việc có một cái nhìn nào đó về mức độ nợ, và nơi cần can thiệp, là quan trọng. Tùy thuộc vào tổ chức của bạn, bạn có thể có thể cung cấp hướng dẫn nhẹ nhàng, nhưng để các đội tự quyết định cách theo dõi và trả nợ. Đối với các tổ chức khác, bạn có thể cần phải có cấu trúc hơn, có lẽ duy trì một nhật ký nợ được xem xét thường xuyên.

#### **Xử lý Ngoại lệ (Exception Handling)**

Vì vậy, các nguyên tắc và thực tiễn của chúng ta hướng dẫn cách hệ thống của chúng ta nên được xây dựng. Nhưng điều gì xảy ra khi hệ thống của chúng ta đi chệch khỏi điều này? Đôi khi chúng ta đưa ra một quyết định chỉ là một ngoại lệ cho quy tắc. Trong những trường hợp này, có thể đáng để ghi lại một quyết định như vậy trong một nhật ký nào đó để tham khảo trong tương lai. Nếu tìm thấy đủ ngoại lệ, cuối cùng có thể có ý nghĩa để thay đổi nguyên tắc hoặc thực tiễn để phản ánh một sự hiểu biết mới về thế giới. Ví dụ, chúng ta có thể có một thực tiễn nói rằng chúng ta sẽ luôn sử dụng MySQL để lưu trữ dữ liệu. Nhưng sau đó chúng ta thấy những lý do thuyết phục để sử dụng Cassandra cho việc lưu trữ có khả năng mở rộng cao, tại thời điểm đó chúng ta thay đổi thực tiễn của mình để nói, "Sử dụng MySQL cho hầu hết các yêu cầu lưu trữ, trừ khi bạn mong đợi sự tăng trưởng lớn về khối lượng, trong trường hợp đó hãy sử dụng Cassandra."

Có lẽ đáng để nhắc lại rằng, mỗi tổ chức đều khác nhau. Tôi đã làm việc với một số công ty nơi các đội phát triển có mức độ tin cậy và tự chủ cao, và ở đó các nguyên tắc rất nhẹ nhàng (và nhu cầu xử lý ngoại lệ rõ ràng bị giảm đi rất nhiều nếu không bị loại bỏ). Trong các tổ chức có cấu trúc hơn, nơi các nhà phát triển có ít tự do hơn, việc theo dõi các ngoại lệ có thể là rất quan trọng để đảm bảo rằng các quy tắc được đặt ra phản ánh đúng những thách thức mà mọi người đang đối mặt. Với tất cả những điều đã nói, tôi là một người hâm mộ microservices như một cách để tối ưu hóa quyền tự chủ của các đội, cho họ càng nhiều tự do càng tốt để giải quyết vấn đề trong tay. Nếu bạn đang làm việc trong một tổ chức đặt ra nhiều hạn chế về cách các nhà phát triển có thể làm việc của họ, thì microservices có thể không dành cho bạn.

#### **Quản trị và Dẫn dắt từ Trung tâm (Governance and Leading from the Center)**

Một phần công việc mà các kiến trúc sư cần xử lý là **quản trị (governance)**. Ý tôi là gì khi nói về quản trị? Hóa ra, **Các Mục tiêu Kiểm soát cho Công nghệ Thông tin và Liên quan (COBIT)** có một định nghĩa khá hay:

> *Quản trị đảm bảo rằng các mục tiêu của doanh nghiệp được đạt được bằng cách đánh giá nhu cầu, điều kiện và lựa chọn của các bên liên quan; thiết lập định hướng thông qua việc ưu tiên hóa và ra quyết định; và giám sát hiệu suất, sự tuân thủ và tiến độ so với định hướng và mục tiêu đã được thống nhất.*
>
> —COBIT 5

Quản trị có thể áp dụng cho nhiều thứ trong diễn đàn CNTT. Chúng ta muốn tập trung vào khía cạnh **quản trị kỹ thuật**, điều mà tôi cảm thấy là công việc của kiến trúc sư. Nếu một trong những công việc của kiến trúc sư là đảm bảo có một tầm nhìn kỹ thuật, thì quản trị là việc đảm bảo những gì chúng ta đang xây dựng phù hợp với tầm nhìn này, và phát triển tầm nhìn nếu cần.

Kiến trúc sư chịu trách nhiệm cho rất nhiều thứ. Họ cần đảm bảo có một bộ nguyên tắc có thể hướng dẫn sự phát triển, và những nguyên tắc này phù hợp với chiến lược của tổ chức. Họ cũng cần đảm bảo rằng những nguyên tắc này không yêu cầu các phương pháp làm việc khiến các nhà phát triển khổ sở. Họ cần cập nhật công nghệ mới, và biết khi nào nên đưa ra những sự đánh đổi đúng đắn. Đây là một trách nhiệm rất lớn. Tất cả những điều đó, và họ cũng cần phải lôi kéo mọi người theo cùng—nghĩa là, để đảm bảo rằng các đồng nghiệp mà họ đang làm việc cùng hiểu được các quyết định đang được đưa ra và được tham gia để thực hiện chúng. Ồ, và như chúng ta đã đề cập: họ cần dành thời gian với các đội để hiểu tác động của các quyết định của họ, và có lẽ thậm chí là viết mã nữa.

Một nhiệm vụ cao cả? Chắc chắn rồi. Nhưng tôi tin chắc rằng họ không nên làm điều này một mình. Một nhóm quản trị hoạt động đúng cách có thể làm việc cùng nhau để chia sẻ công việc và định hình tầm nhìn.

Thông thường, quản trị là một hoạt động nhóm. Nó có thể là một cuộc trò chuyện không chính thức với một đội đủ nhỏ, hoặc một cuộc họp định kỳ có cấu trúc hơn với tư cách thành viên nhóm chính thức cho một phạm vi lớn hơn. Đây là nơi tôi nghĩ rằng các nguyên tắc mà chúng ta đã đề cập trước đó nên được thảo luận và thay đổi khi cần thiết. Nhóm này cần được dẫn dắt bởi một nhà công nghệ, và bao gồm chủ yếu những người đang thực hiện công việc được quản trị. Nhóm này cũng nên chịu trách nhiệm theo dõi và quản lý các rủi ro kỹ thuật.

Một mô hình mà tôi rất ủng hộ là để kiến trúc sư chủ trì nhóm, nhưng có phần lớn nhóm được lấy từ các nhà công nghệ của mỗi đội giao hàng—tối thiểu là các trưởng nhóm của mỗi đội. Kiến trúc sư chịu trách nhiệm đảm bảo nhóm hoạt động, nhưng toàn bộ nhóm chịu trách nhiệm về quản trị. Điều này chia sẻ gánh nặng, và đảm bảo rằng có một mức độ chấp nhận cao hơn. Nó cũng đảm bảo rằng thông tin chảy tự do từ các đội vào nhóm, và kết quả là, việc ra quyết định trở nên hợp lý và có thông tin hơn nhiều.

Đôi khi, nhóm có thể đưa ra các quyết định mà kiến trúc sư không đồng ý. Tại thời điểm này, kiến trúc sư phải làm gì? Đã từng ở trong tình huống này trước đây, tôi có thể nói với bạn đây là một trong những tình huống khó khăn nhất phải đối mặt. Thường thì, tôi chọn cách tiếp cận là tôi nên đi theo quyết định của nhóm. Tôi có quan điểm rằng tôi đã cố gắng hết sức để thuyết phục mọi người, nhưng cuối cùng tôi đã không đủ thuyết phục. Nhóm thường khôn ngoan hơn cá nhân, và tôi đã được chứng minh là sai nhiều hơn một lần! Và hãy tưởng tượng việc một nhóm đã được trao không gian để đưa ra một quyết định, và sau đó cuối cùng bị phớt lờ, sẽ làm mất tinh thần như thế nào. Nhưng đôi khi tôi đã bác bỏ quyết định của nhóm. Nhưng tại sao, và khi nào? Bạn chọn ranh giới như thế nào?

Hãy nghĩ về việc dạy một đứa trẻ đi xe đạp. Bạn không thể đi xe thay cho chúng. Bạn quan sát chúng loạng choạng, nhưng nếu bạn can thiệp mỗi khi có vẻ như chúng có thể ngã, thì chúng sẽ không bao giờ học được, và trong mọi trường hợp, chúng ngã ít hơn nhiều so với bạn nghĩ! Nhưng nếu bạn thấy chúng sắp lao vào dòng xe cộ, hoặc vào một cái ao vịt gần đó, thì bạn phải can thiệp. Tương tự như vậy, với tư cách là một kiến trúc sư, bạn cần phải có một sự nắm bắt chắc chắn về việc khi nào, theo nghĩa bóng, đội của bạn đang lái xe vào một cái ao vịt. Bạn cũng cần nhận thức được rằng ngay cả khi bạn biết mình đúng và bác bỏ quyết định của đội, điều này có thể làm suy yếu vị trí của bạn và cũng làm cho đội cảm thấy rằng họ không có tiếng nói. Đôi khi điều đúng đắn là đi theo một quyết định mà bạn không đồng ý. Việc biết khi nào nên làm điều này và khi nào không là rất khó, nhưng đôi khi lại rất quan trọng.

---
#### **Xây dựng một Đội (Building a Team)**

Là người chịu trách nhiệm chính cho tầm nhìn kỹ thuật của hệ thống và đảm bảo bạn đang thực hiện tầm nhìn này không chỉ là về việc đưa ra các quyết định công nghệ. Chính những người bạn làm việc cùng sẽ là người thực hiện công việc. Phần lớn vai trò của một người lãnh đạo kỹ thuật là về việc giúp họ phát triển—để giúp họ tự hiểu tầm nhìn—và cũng đảm bảo rằng họ có thể là những người tham gia tích cực trong việc định hình và thực hiện tầm nhìn đó.

Giúp đỡ những người xung quanh bạn trong sự phát triển sự nghiệp của riêng họ có thể có nhiều hình thức, hầu hết trong số đó nằm ngoài phạm vi của cuốn sách này. Tuy nhiên, có một khía cạnh mà một kiến trúc microservice đặc biệt phù hợp. Với các hệ thống `monolithic` lớn hơn, có ít cơ hội hơn để mọi người đứng lên và sở hữu một cái gì đó. Mặt khác, với microservices, chúng ta có nhiều `codebase` tự chủ sẽ có vòng đời độc lập riêng. Giúp mọi người đứng lên bằng cách để họ chịu trách nhiệm sở hữu các dịch vụ cá nhân trước khi chấp nhận nhiều trách nhiệm hơn có thể là một cách tuyệt vời để giúp họ đạt được các mục tiêu sự nghiệp của riêng mình, và đồng thời giảm bớt gánh nặng cho bất kỳ ai đang phụ trách!

Tôi là một người tin tưởng mạnh mẽ rằng phần mềm tuyệt vời đến từ những con người tuyệt vời. Nếu bạn chỉ lo lắng về mặt công nghệ của phương trình, bạn đang bỏ lỡ hơn một nửa bức tranh.

---
#### **Tóm tắt (Summary)**

Để tóm tắt chương này, đây là những gì tôi thấy là trách nhiệm cốt lõi của kiến trúc sư tiến hóa:

*   **Tầm nhìn (Vision)**
    Đảm bảo có một tầm nhìn kỹ thuật được truyền đạt rõ ràng cho hệ thống sẽ giúp hệ thống của bạn đáp ứng các yêu cầu của khách hàng và tổ chức của bạn.
*   **Sự đồng cảm (Empathy)**
    Hiểu được tác động của các quyết định của bạn đối với khách hàng và đồng nghiệp của bạn.
*   **Sự hợp tác (Collaboration)**
    Tham gia với càng nhiều đồng nghiệp và đồng nghiệp của bạn càng tốt để giúp định hình, tinh chỉnh và thực hiện tầm nhìn.
*   **Khả năng thích ứng (Adaptability)**
    Đảm bảo rằng tầm nhìn kỹ thuật thay đổi khi khách hàng hoặc tổ chức của bạn yêu cầu.
*   **Quyền tự chủ (Autonomy)**
    Tìm sự cân bằng phù hợp giữa việc chuẩn hóa và cho phép quyền tự chủ cho các đội của bạn.
*   **Quản trị (Governance)**
    Đảm bảo rằng hệ thống đang được triển khai phù hợp với tầm nhìn kỹ thuật.

Kiến trúc sư tiến hóa là người hiểu rằng việc thực hiện kỳ công này là một hành động cân bằng liên tục. Các lực lượng luôn đẩy bạn theo hướng này hay hướng khác, và việc hiểu nơi để đẩy lùi hoặc nơi để xuôi theo dòng chảy thường là điều chỉ có được qua kinh nghiệm. Nhưng phản ứng tồi tệ nhất đối với tất cả các lực lượng đẩy chúng ta về phía sự thay đổi là trở nên cứng nhắc hoặc cố định hơn trong suy nghĩ của chúng ta.

Trong khi phần lớn lời khuyên trong chương này có thể áp dụng cho bất kỳ kiến trúc sư hệ thống nào, microservices cho chúng ta nhiều quyết định hơn để đưa ra. Do đó, việc có khả năng cân bằng tốt hơn tất cả những sự đánh đổi này là điều cần thiết.

Trong chương tiếp theo, chúng ta sẽ mang theo một số nhận thức mới tìm thấy của mình về vai trò của kiến trúc sư khi chúng ta bắt đầu suy nghĩ về cách tìm ra các ranh giới phù hợp cho các microservice của mình.

---

### **3. Cách Mô hình hóa Dịch vụ (How to Model Services)**

> *Lý luận của đối thủ của tôi làm tôi nhớ đến người ngoại đạo, người khi được hỏi thế giới đứng trên cái gì, đã trả lời, “Trên một con rùa.” Nhưng con rùa đứng trên cái gì? “Trên một con rùa khác.”*
>
> —Joseph Barker (1854)

Vậy là bạn đã biết microservices là gì, và hy vọng có một cảm nhận về những lợi ích chính của chúng. Có lẽ bây giờ bạn đang háo hức bắt tay vào và bắt đầu tạo ra chúng, phải không? Nhưng bắt đầu từ đâu? Trong chương này, chúng ta sẽ xem xét cách suy nghĩ về các ranh giới của microservices của bạn để hy vọng tối đa hóa được những mặt tích cực và tránh được một số nhược điểm tiềm tàng. Nhưng trước tiên, chúng ta cần một cái gì đó để làm việc.

#### **Giới thiệu MusicCorp**

Những cuốn sách về ý tưởng hoạt động tốt hơn với các ví dụ. Khi có thể, tôi sẽ chia sẻ những câu chuyện từ các tình huống thực tế, nhưng tôi thấy rằng việc có một lĩnh vực nghiệp vụ hư cấu để làm việc cũng rất hữu ích. Trong suốt cuốn sách, chúng ta sẽ quay lại lĩnh vực này, xem xét khái niệm microservices hoạt động như thế nào trong thế giới này.

Vậy hãy chuyển sự chú ý của chúng ta đến nhà bán lẻ trực tuyến tiên tiến MusicCorp. MusicCorp gần đây là một nhà bán lẻ truyền thống (brick-and-mortar), nhưng sau khi việc kinh doanh đĩa than sụp đổ, họ đã tập trung ngày càng nhiều nỗ lực vào thế giới trực tuyến. Công ty có một trang web, nhưng cảm thấy rằng bây giờ là lúc để dồn toàn lực vào thế giới trực tuyến. Rốt cuộc, những chiếc iPod đó chỉ là một mốt nhất thời (rõ ràng là Zunes tốt hơn nhiều) và những người hâm mộ âm nhạc khá vui lòng chờ đợi đĩa CD được giao đến tận nhà. Chất lượng hơn sự tiện lợi, phải không? Và trong khi chúng ta đang nói về nó, cái thứ Spotify mà mọi người cứ nhắc đến là gì vậy—một loại thuốc trị mụn cho thanh thiếu niên à?

Mặc dù hơi chậm chân, MusicCorp có những tham vọng lớn. May mắn thay, họ đã quyết định rằng cơ hội tốt nhất để chiếm lĩnh thế giới là đảm bảo họ có thể thực hiện các thay đổi một cách dễ dàng nhất có thể. Microservices thẳng tiến!

#### **Điều gì tạo nên một Dịch vụ tốt?**

Trước khi đội ngũ từ MusicCorp lao vào cuộc, tạo ra dịch vụ này đến dịch vụ khác trong nỗ lực cung cấp băng 8-track cho tất cả mọi người, hãy cùng phanh lại và nói một chút về ý tưởng cơ bản quan trọng nhất mà chúng ta cần ghi nhớ. Điều gì tạo nên một dịch vụ tốt? Nếu bạn đã sống sót qua một lần triển khai SOA thất bại, bạn có thể có một số ý tưởng về nơi tôi sẽ đi tiếp theo. Nhưng trong trường hợp bạn không (kém) may mắn như vậy, tôi muốn bạn tập trung vào hai khái niệm chính: **ghép nối lỏng lẻo (loose coupling)** và **gắn kết cao (high cohesion)**. Chúng ta sẽ nói chi tiết trong suốt cuốn sách về các ý tưởng và thực tiễn khác, nhưng tất cả chúng sẽ trở nên vô nghĩa nếu chúng ta làm sai hai điều này.

Mặc dù hai thuật ngữ này được sử dụng rất nhiều, đặc biệt là trong bối cảnh của các hệ thống hướng đối tượng, nhưng đáng để thảo luận về ý nghĩa của chúng trong bối cảnh của microservices.

##### **Ghép nối Lỏng lẻo (Loose Coupling)**

Khi các dịch vụ được **ghép nối lỏng lẻo**, một thay đổi đối với một dịch vụ không nên yêu cầu một thay đổi đối với một dịch vụ khác. Toàn bộ ý nghĩa của một microservice là có thể thực hiện một thay đổi đối với một dịch vụ và triển khai nó, mà không cần phải thay đổi bất kỳ phần nào khác của hệ thống. Điều này thực sự rất quan trọng.

Những loại điều gì gây ra **ghép nối chặt chẽ (tight coupling)**? Một sai lầm kinh điển là chọn một kiểu tích hợp ràng buộc chặt chẽ một dịch vụ này với một dịch vụ khác, gây ra các thay đổi bên trong dịch vụ yêu cầu một thay đổi đối với người tiêu dùng. Chúng ta sẽ thảo luận về cách tránh điều này sâu hơn trong **Chương 4**.

Một dịch vụ được ghép nối lỏng lẻo biết càng ít càng tốt về các dịch vụ mà nó hợp tác. Điều này cũng có nghĩa là chúng ta có thể muốn hạn chế số lượng các loại cuộc gọi khác nhau từ một dịch vụ đến dịch vụ khác, bởi vì ngoài vấn đề hiệu suất tiềm tàng, giao tiếp "lắm lời" (`chatty communication`) có thể dẫn đến ghép nối chặt chẽ.

##### **Gắn kết Cao (High Cohesion)**

Chúng ta muốn các hành vi liên quan ở cùng một nơi, và các hành vi không liên quan ở nơi khác. Tại sao? Chà, nếu chúng ta muốn thay đổi hành vi, chúng ta muốn có thể thay đổi nó ở một nơi, và phát hành thay đổi đó càng sớm càng tốt. Nếu chúng ta phải thay đổi hành vi đó ở nhiều nơi khác nhau, chúng ta sẽ phải phát hành nhiều dịch vụ khác nhau (có lẽ là cùng một lúc) để cung cấp thay đổi đó. Việc thực hiện thay đổi ở nhiều nơi khác nhau sẽ chậm hơn, và việc triển khai nhiều dịch vụ cùng một lúc là rủi ro—cả hai điều này chúng ta đều muốn tránh.

Vì vậy, chúng ta muốn tìm các ranh giới trong miền vấn đề của mình giúp đảm bảo rằng hành vi liên quan ở một nơi, và giao tiếp với các ranh giới khác một cách lỏng lẻo nhất có thể.

---
#### **Bounded Context**

Cuốn sách *Domain-Driven Design* của Eric Evans (Addison-Wesley) tập trung vào cách tạo ra các hệ thống mô hình hóa các miền nghiệp vụ trong thế giới thực. Cuốn sách đầy những ý tưởng tuyệt vời như sử dụng ngôn ngữ phổ quát (ubiquitous language), các trừu tượng `repository`, và những thứ tương tự, nhưng có một khái niệm rất quan trọng mà Evans giới thiệu mà lúc đầu tôi đã hoàn toàn bỏ qua: **bounded context** (bối cảnh bị giới hạn). Ý tưởng là bất kỳ miền nghiệp vụ nào cũng bao gồm nhiều `bounded context`, và nằm trong mỗi `context` là những thứ (Eric sử dụng từ *mô hình (model)* rất nhiều, có lẽ tốt hơn từ *những thứ*) không cần phải được giao tiếp ra bên ngoài cũng như những thứ được chia sẻ bên ngoài với các `bounded context` khác. Mỗi `bounded context` có một giao diện rõ ràng, nơi nó quyết định những mô hình nào sẽ chia sẻ với các `context` khác.

Một định nghĩa khác về `bounded context` mà tôi thích là "một trách nhiệm cụ thể được thực thi bởi các ranh giới rõ ràng." Nếu bạn muốn thông tin từ một `bounded context`, hoặc muốn yêu cầu chức năng trong một `bounded context`, bạn giao tiếp với ranh giới rõ ràng của nó bằng cách sử dụng các mô hình. Trong cuốn sách của mình, Evans sử dụng phép loại suy về các tế bào, trong đó "[c]ác tế bào có thể tồn tại bởi vì màng của chúng xác định những gì ở trong và ngoài và quyết định những gì có thể đi qua."

Hãy quay lại một chút với doanh nghiệp MusicCorp. Miền nghiệp vụ của chúng ta là toàn bộ doanh nghiệp mà chúng ta đang hoạt động. Nó bao gồm mọi thứ từ nhà kho đến quầy lễ tân, từ tài chính đến đặt hàng. Chúng ta có thể mô hình hóa hoặc không mô hình hóa tất cả những điều đó trong phần mềm của mình, nhưng đó vẫn là miền nghiệp vụ mà chúng ta đang hoạt động. Hãy suy nghĩ về các phần của miền nghiệp vụ đó trông giống như các `bounded context` mà Evans đề cập đến. Tại MusicCorp, nhà kho của chúng ta là một nơi hoạt động sôi nổi—quản lý các đơn hàng được vận chuyển đi (và thỉnh thoảng có hàng trả lại), nhận hàng mới, tổ chức các cuộc đua xe nâng, và vân vân. Ở nơi khác, phòng tài chính có lẽ ít vui vẻ hơn, nhưng vẫn có một chức năng rất quan trọng bên trong tổ chức của chúng ta. Những nhân viên này quản lý bảng lương, giữ sổ sách kế toán của công ty, và tạo ra các báo cáo quan trọng. Rất nhiều báo cáo. Họ có lẽ cũng có những món đồ chơi bàn làm việc thú vị.

##### **Mô hình Dùng chung và Ẩn (Shared and Hidden Models)**

Đối với MusicCorp, chúng ta có thể coi phòng tài chính và nhà kho là hai `bounded context` riêng biệt. Cả hai đều có một giao diện rõ ràng với thế giới bên ngoài (về các báo cáo hàng tồn kho, phiếu lương, v.v.), và chúng có những chi tiết mà chỉ chúng mới cần biết (xe nâng, máy tính).

Bây giờ phòng tài chính không cần biết về các hoạt động chi tiết bên trong của nhà kho. Tuy nhiên, nó cần biết một số thứ—ví dụ, nó cần biết về mức tồn kho để cập nhật sổ sách kế toán. Hình 3-1 cho thấy một ví dụ về sơ đồ `context`. Chúng ta thấy các khái niệm là nội bộ của nhà kho, như Người lấy hàng (Picker - người lấy đơn hàng), kệ đại diện cho các vị trí kho, và vân vân. Tương tự, sổ cái chung của công ty là không thể thiếu đối với tài chính nhưng không được chia sẻ ra bên ngoài ở đây.

![alt text](<images/Screenshot from 2025-09-29 06-48-34.png>)

**Hình 3-1.** *Một mô hình được chia sẻ giữa phòng tài chính và nhà kho*

Tuy nhiên, để có thể tính toán được giá trị của công ty, các nhân viên tài chính cần thông tin về hàng tồn kho mà chúng ta đang nắm giữ. Mục hàng tồn kho sau đó trở thành một mô hình được chia sẻ giữa hai `context`. Tuy nhiên, lưu ý rằng chúng ta không cần phải phơi bày một cách mù quáng mọi thứ về mục hàng tồn kho từ `context` nhà kho. Ví dụ, mặc dù bên trong chúng ta giữ một bản ghi trên một mục hàng tồn kho về nơi nó nên ở trong nhà kho, điều đó không cần phải được phơi bày trong mô hình được chia sẻ. Vì vậy, có biểu diễn chỉ nội bộ, và biểu diễn bên ngoài mà chúng ta phơi bày. Theo nhiều cách, điều này báo trước cuộc thảo luận xung quanh `REST` trong **Chương 4**.

Đôi khi chúng ta cũng có thể gặp các mô hình có cùng tên nhưng có ý nghĩa rất khác nhau trong các `context` khác nhau. Ví dụ, chúng ta có thể có khái niệm về *hàng trả lại (return)*, đại diện cho việc một khách hàng gửi lại một cái gì đó. Trong `context` của khách hàng, một *hàng trả lại* là tất cả về việc in nhãn vận chuyển, gửi một gói hàng và chờ hoàn tiền. Đối với nhà kho, điều này có thể đại diện cho một gói hàng sắp đến, và một mục hàng tồn kho cần được nhập lại kho. Theo đó, trong nhà kho, chúng ta lưu trữ thông tin bổ sung liên quan đến *hàng trả lại* liên quan đến các nhiệm vụ cần thực hiện; ví dụ, chúng ta có thể tạo ra một yêu cầu nhập lại kho. Mô hình được chia sẻ của *hàng trả lại* trở nên liên quan đến các quy trình và thực thể hỗ trợ khác nhau trong mỗi `bounded context`, nhưng đó là một mối quan tâm rất nội bộ trong chính `context` đó.

#### **Modules và Services**

Bằng cách suy nghĩ rõ ràng về những mô hình nào nên được chia sẻ, và không chia sẻ các biểu diễn nội bộ của chúng ta, chúng ta tránh được một trong những cạm bẫy tiềm tàng có thể dẫn đến **ghép nối chặt chẽ (`tight coupling`)** (điều ngược lại với những gì chúng ta muốn). Chúng ta cũng đã xác định được một ranh giới trong miền nghiệp vụ của mình, nơi tất cả các khả năng nghiệp vụ tương tự nên tồn tại, mang lại cho chúng ta **tính gắn kết cao (`high cohesion`)** mà chúng ta mong muốn. Do đó, các `bounded context` này cực kỳ phù hợp để trở thành các ranh giới thành phần.

Như chúng ta đã thảo luận trong **Chương 1**, chúng ta có tùy chọn sử dụng các **module** trong ranh giới của một tiến trình để giữ các mã liên quan lại với nhau và cố gắng giảm sự ghép nối với các module khác trong hệ thống. Khi bạn bắt đầu với một `codebase` mới, đây có lẽ là một nơi tốt để bắt đầu. Vì vậy, một khi bạn đã tìm thấy các `bounded context` trong miền nghiệp vụ của mình, hãy đảm bảo chúng được mô hình hóa trong `codebase` của bạn dưới dạng các `module`, với các mô hình được chia sẻ và ẩn.

Những ranh giới `module` này sau đó trở thành những ứng cử viên xuất sắc cho các microservices. Nói chung, các microservices nên được **căn chỉnh rõ ràng với các `bounded context`**. Khi bạn đã trở nên rất thành thạo, bạn có thể quyết định bỏ qua bước giữ `bounded context` được mô hình hóa như một `module` trong một hệ thống `monolithic` hơn, và nhảy thẳng đến một dịch vụ riêng biệt. Tuy nhiên, khi mới bắt đầu, hãy giữ một hệ thống mới ở phía `monolithic` hơn; việc xác định sai ranh giới dịch vụ có thể tốn kém, vì vậy việc chờ đợi mọi thứ ổn định khi bạn làm quen với một miền nghiệp vụ mới là hợp lý. Chúng ta sẽ thảo luận nhiều hơn về điều này trong **Chương 5**, cùng với các kỹ thuật giúp chia nhỏ các hệ thống hiện có thành các microservices.

Vì vậy, nếu ranh giới dịch vụ của chúng ta phù hợp với các `bounded context` trong miền nghiệp vụ, và các microservices của chúng ta đại diện cho các `bounded context` đó, chúng ta đang có một khởi đầu tuyệt vời trong việc đảm bảo rằng các microservices của chúng ta được ghép nối lỏng lẻo và có tính gắn kết mạnh mẽ.

##### **Phân tách quá sớm (Premature Decomposition)**

Tại ThoughtWorks, chúng tôi đã tự mình trải nghiệm những thách thức của việc chia nhỏ các microservice quá nhanh. Ngoài việc tư vấn, chúng tôi cũng tạo ra một vài sản phẩm. Một trong số đó là SnapCI, một công cụ tích hợp liên tục và phân phối liên tục được lưu trữ (chúng ta sẽ thảo luận về những khái niệm đó sau trong **Chương 6**). Đội ngũ này trước đây đã làm việc trên một công cụ tương tự khác, GoCD, một công cụ phân phối liên tục mã nguồn mở hiện nay có thể được triển khai cục bộ thay vì được lưu trữ trên đám mây.

Mặc dù có một số mã được tái sử dụng rất sớm giữa các dự án SnapCI và GoCD, cuối cùng SnapCI đã trở thành một `codebase` hoàn toàn mới. Tuy nhiên, kinh nghiệm trước đây của đội ngũ trong lĩnh vực công cụ CD đã khuyến khích họ di chuyển nhanh hơn trong việc xác định các ranh giới, và xây dựng hệ thống của họ như một tập hợp các microservices.

Tuy nhiên, sau một vài tháng, rõ ràng là các trường hợp sử dụng của SnapCI khác biệt một cách tinh tế đến mức việc tiếp cận ban đầu về ranh giới dịch vụ không hoàn toàn đúng. Điều này dẫn đến rất nhiều thay đổi được thực hiện trên các dịch vụ, và một chi phí thay đổi cao liên quan. Cuối cùng, đội ngũ đã hợp nhất các dịch vụ trở lại thành một hệ thống `monolithic` duy nhất, cho họ thời gian để hiểu rõ hơn về nơi các ranh giới nên tồn tại. Một năm sau, đội ngũ sau đó đã có thể chia nhỏ hệ thống `monolithic` thành các microservices, với các ranh giới đã được chứng minh là ổn định hơn nhiều. Đây không phải là ví dụ duy nhất về tình huống này mà tôi đã thấy. Việc phân tách một hệ thống thành các microservices quá sớm có thể tốn kém, đặc biệt nếu bạn mới làm quen với lĩnh vực này. Theo nhiều cách, việc có một `codebase` hiện có mà bạn muốn phân tách thành các microservices dễ dàng hơn nhiều so với việc cố gắng chuyển sang microservices ngay từ đầu.

#### **Khả năng Nghiệp vụ (Business Capabilities)**

Khi bạn bắt đầu suy nghĩ về các `bounded context` tồn tại trong tổ chức của mình, bạn nên suy nghĩ không phải về dữ liệu được chia sẻ, mà về các **khả năng (capabilities)** mà các `context` đó cung cấp cho phần còn lại của miền nghiệp vụ. Nhà kho có thể cung cấp khả năng lấy danh sách hàng tồn kho hiện tại, chẳng hạn, hoặc `context` tài chính có thể phơi bày tài khoản cuối tháng hoặc cho phép bạn thiết lập bảng lương cho một nhân viên mới. Những khả năng này có thể yêu cầu trao đổi thông tin—các mô hình được chia sẻ—nhưng tôi đã thấy quá thường xuyên rằng việc suy nghĩ về dữ liệu dẫn đến các dịch vụ `anemic`, dựa trên CRUD (tạo, đọc, cập nhật, xóa). Vì vậy, hãy hỏi trước "Context này làm gì?", và sau đó "Vậy nó cần dữ liệu gì để làm điều đó?"

Khi được mô hình hóa dưới dạng dịch vụ, những khả năng này trở thành các hoạt động chính sẽ được phơi bày qua dây (`over the wire`) cho các cộng tác viên khác.

#### **Rùa chồng lên nhau (Turtles All the Way Down)**

Ban đầu, bạn có thể sẽ xác định được một số `bounded context` ở mức độ thô. Nhưng những `bounded context` này lần lượt có thể chứa các `bounded context` khác. Ví dụ, bạn có thể phân tách nhà kho thành các khả năng liên quan đến việc hoàn thành đơn hàng, quản lý hàng tồn kho, hoặc nhận hàng. Khi xem xét các ranh giới của microservices của bạn, trước tiên hãy nghĩ về các `context` lớn hơn, thô hơn, và sau đó chia nhỏ dọc theo các `context` lồng nhau này khi bạn đang tìm kiếm lợi ích của việc chia nhỏ các đường nối (`seams`) này.

Tôi đã thấy các `context` lồng nhau này vẫn được ẩn đối với các microservice khác, đang hợp tác, mang lại hiệu quả lớn. Đối với thế giới bên ngoài, họ vẫn đang sử dụng các khả năng nghiệp vụ trong nhà kho, nhưng họ không biết rằng các yêu cầu của họ thực sự đang được ánh xạ một cách minh bạch đến hai hoặc nhiều dịch vụ riêng biệt, như bạn có thể thấy trong Hình 3-2. Đôi khi, bạn sẽ quyết định rằng việc `bounded context` cấp cao hơn không được mô hình hóa rõ ràng như một ranh giới dịch vụ sẽ hợp lý hơn, như trong Hình 3-3, vì vậy thay vì một ranh giới nhà kho duy nhất, bạn có thể thay vào đó chia nhỏ ra hàng tồn kho, hoàn thành đơn hàng, và nhận hàng.

![alt text](<images/Screenshot from 2025-09-29 06-48-49.png>)

**Hình 3-2.** *Microservices đại diện cho các bounded context lồng nhau được ẩn bên trong nhà kho*

![alt text](<images/Screenshot from 2025-09-29 06-48-58.png>)

**Hình 3-3.** *Các bounded context bên trong nhà kho được đưa lên thành các context cấp cao của riêng chúng*

Nói chung, không có một quy tắc cứng nhắc nào về cách tiếp cận nào là hợp lý nhất. Tuy nhiên, việc bạn chọn cách tiếp cận lồng nhau hay cách tiếp cận tách biệt hoàn toàn nên dựa trên cấu trúc tổ chức của bạn. Nếu việc hoàn thành đơn hàng, quản lý hàng tồn kho, và nhận hàng được quản lý bởi các đội khác nhau, chúng có lẽ xứng đáng với vị thế là các microservice cấp cao. Mặt khác, nếu tất cả chúng đều được quản lý bởi một đội, thì mô hình lồng nhau sẽ hợp lý hơn. Điều này là do sự tương tác của các cấu trúc tổ chức và kiến trúc phần mềm, mà chúng ta sẽ thảo luận ở cuối cuốn sách trong **Chương 10**.

Một lý do khác để ưa thích cách tiếp cận lồng nhau có thể là để phân chia kiến trúc của bạn nhằm đơn giản hóa việc kiểm thử. Ví dụ, khi kiểm thử các dịch vụ tiêu thụ nhà kho, tôi không cần phải `stub` từng dịch vụ bên trong `context` nhà kho, chỉ cần API thô hơn. Điều này cũng có thể cung cấp cho bạn một đơn vị cô lập khi xem xét các bài kiểm thử có phạm vi lớn hơn. Ví dụ, tôi có thể quyết định có các bài kiểm thử đầu cuối (`end-to-end tests`) nơi tôi khởi chạy tất cả các dịch vụ bên trong `context` nhà kho, nhưng đối với tất cả các cộng tác viên khác, tôi có thể `stub` chúng ra. Chúng ta sẽ khám phá thêm về kiểm thử và sự cô lập trong **Chương 7**.

#### **Giao tiếp theo các Khái niệm Nghiệp vụ (Communication in Terms of Business Concepts)**

Những thay đổi chúng ta thực hiện đối với hệ thống của mình thường là về những thay đổi mà doanh nghiệp muốn thực hiện đối với cách hệ thống hoạt động. Chúng ta đang thay đổi chức năng—các khả năng—được phơi bày cho khách hàng của mình. Nếu hệ thống của chúng ta được phân tách dọc theo các `bounded context` đại diện cho miền nghiệp vụ của mình, những thay đổi chúng ta muốn thực hiện có nhiều khả năng được cô lập vào một, ranh giới microservice duy nhất. Điều này làm giảm số lượng nơi chúng ta cần thực hiện một thay đổi, và cho phép chúng ta triển khai thay đổi đó một cách nhanh chóng.

Cũng rất quan trọng khi nghĩ về giao tiếp giữa các microservice này theo cùng các khái niệm nghiệp vụ. Việc mô hình hóa phần mềm của bạn theo miền nghiệp vụ không nên dừng lại ở ý tưởng về `bounded context`. Các thuật ngữ và ý tưởng giống nhau được chia sẻ giữa các bộ phận của tổ chức của bạn nên được phản ánh trong các giao diện của bạn. Có thể hữu ích khi nghĩ về các biểu mẫu được gửi giữa các microservice này, giống như các biểu mẫu được gửi xung quanh trong một tổ chức.

#### **Ranh giới Kỹ thuật (The Technical Boundary)**

Việc xem xét những gì có thể đi sai khi các dịch vụ được mô hình hóa không chính xác có thể hữu ích. Cách đây một thời gian, một vài đồng nghiệp và tôi đang làm việc với một khách hàng ở California, giúp công ty áp dụng một số thực hành mã sạch hơn và chuyển sang kiểm thử tự động nhiều hơn. Chúng tôi đã bắt đầu với một số quả treo thấp, chẳng hạn như phân tách dịch vụ, khi chúng tôi nhận thấy một điều gì đó đáng lo ngại hơn nhiều. Tôi không thể đi sâu vào chi tiết về những gì ứng dụng đã làm, nhưng đó là một ứng dụng hướng tới công chúng với một lượng lớn khách hàng toàn cầu.

Đội ngũ, và hệ thống, đã phát triển. Ban đầu là tầm nhìn của một người, hệ thống đã đảm nhận ngày càng nhiều tính năng, và ngày càng nhiều người dùng. Cuối cùng, tổ chức đã quyết định tăng năng lực của đội bằng cách có một nhóm nhà phát triển mới có trụ sở tại Brazil đảm nhận một số công việc. Hệ thống được chia nhỏ, với nửa đầu của ứng dụng về cơ bản là không trạng thái (`stateless`), thực hiện trang web hướng tới công chúng, như được hiển thị trong Hình 3-4. Nửa sau của hệ thống chỉ đơn giản là một giao diện gọi thủ tục từ xa (`remote procedure call - RPC`) trên một kho dữ liệu. Về cơ bản, hãy tưởng tượng bạn đã lấy một lớp `repository` trong `codebase` của mình và biến nó thành một dịch vụ riêng biệt.

![alt text](<images/Screenshot from 2025-09-29 06-49-07.png>)

**Hình 3-4.** *Một ranh giới dịch vụ được chia cắt theo một đường nối kỹ thuật*

Các thay đổi thường xuyên phải được thực hiện cho cả hai dịch vụ. Cả hai dịch vụ đều nói chuyện theo các cuộc gọi phương thức kiểu `RPC` cấp thấp, quá mong manh (chúng ta sẽ thảo luận sâu hơn về điều này trong **Chương 4**). Giao diện dịch vụ cũng rất "lắm lời", dẫn đến các vấn đề về hiệu suất. Điều này dẫn đến nhu cầu về các cơ chế gộp `RPC` phức tạp. Tôi gọi đây là **kiến trúc củ hành (onion architecture)**, vì nó có nhiều lớp và làm tôi khóc khi phải cắt xuyên qua nó.

Bây giờ về bề ngoài, ý tưởng chia nhỏ hệ thống nguyên khối trước đây dọc theo các ranh giới địa lý/tổ chức là hoàn toàn hợp lý, như chúng ta sẽ mở rộng trong **Chương 10**. Tuy nhiên, ở đây, thay vì lấy một lát cắt dọc, tập trung vào nghiệp vụ thông qua `stack`, đội ngũ đã chọn những gì trước đây là một API trong tiến trình và tạo ra một lát cắt ngang.

Việc đưa ra quyết định mô hình hóa các ranh giới dịch vụ dọc theo các đường nối kỹ thuật không phải lúc nào cũng sai. Tôi chắc chắn đã thấy điều này rất có ý nghĩa khi một tổ chức đang tìm cách đạt được các mục tiêu hiệu suất nhất định, chẳng hạn. Tuy nhiên, nó nên là động lực thứ cấp của bạn để tìm kiếm những đường nối này, không phải là động lực chính của bạn.

#### **Tóm tắt (Summary)**

Trong chương này, bạn đã học được một chút về những gì tạo nên một dịch vụ tốt, và cách tìm ra các đường nối trong không gian vấn đề của chúng ta mang lại cho chúng ta lợi ích kép của cả việc ghép nối lỏng lẻo và gắn kết cao. Các `bounded context` là một công cụ quan trọng giúp chúng ta tìm thấy những đường nối này, và bằng cách căn chỉnh các microservices của chúng ta với những ranh giới này, chúng ta đảm bảo rằng hệ thống kết quả có mọi cơ hội để giữ nguyên những đức tính đó. Chúng ta cũng đã có một gợi ý về cách chúng ta có thể chia nhỏ các microservices của mình hơn nữa, một điều chúng ta sẽ khám phá sâu hơn sau này. Và chúng ta cũng đã giới thiệu MusicCorp, miền nghiệp vụ ví dụ mà chúng ta sẽ sử dụng trong suốt cuốn sách này.

Các ý tưởng được trình bày trong *Domain-Driven Design* của Eric Evans rất hữu ích cho chúng ta trong việc tìm kiếm các ranh giới hợp lý cho các dịch vụ của mình, và tôi chỉ mới gãi nhẹ bề mặt ở đây. Tôi khuyên bạn nên đọc cuốn sách *Implementing Domain-Driven Design* của Vaughn Vernon (Addison-Wesley) để giúp bạn hiểu được các khía cạnh thực tế của cách tiếp cận này.

Mặc dù chương này chủ yếu ở mức độ cao, chúng ta cần phải đi sâu hơn về mặt kỹ thuật trong chương tiếp theo. Có rất nhiều cạm bẫy liên quan đến việc triển khai các giao diện giữa các dịch vụ có thể dẫn đến đủ loại rắc rối, và chúng ta sẽ phải đi sâu vào chủ đề này nếu chúng ta muốn giữ cho hệ thống của mình không trở thành một mớ hỗn độn, rối rắm khổng lồ.

---

### **4. Tích hợp (Integration)**

Theo quan điểm của tôi, việc tích hợp đúng cách là khía cạnh công nghệ quan trọng nhất liên quan đến microservices. Làm tốt, và các microservice của bạn sẽ giữ được quyền tự chủ, cho phép bạn thay đổi và phát hành chúng độc lập với toàn bộ hệ thống. Làm sai, và thảm họa đang chờ đợi. Hy vọng rằng sau khi bạn đọc xong chương này, bạn sẽ học được cách tránh một số cạm bẫy lớn nhất đã gây khó khăn cho các nỗ lực SOA khác và vẫn có thể đang chờ đợi bạn trên hành trình đến với microservices.

#### **Tìm kiếm Công nghệ Tích hợp Lý tưởng (Looking for the Ideal Integration Technology)**

Có một loạt các lựa chọn đáng kinh ngạc về cách một microservice có thể nói chuyện với một microservice khác. Nhưng cái nào là đúng: `SOAP`? `XML-RPC`? `REST`? `Protocol buffers`? Chúng ta sẽ đi sâu vào những thứ đó trong giây lát, nhưng trước khi làm vậy, hãy suy nghĩ về những gì chúng ta muốn từ bất kỳ công nghệ nào chúng ta chọn.

##### **Tránh những Thay đổi Gây đổ vỡ (Avoid Breaking Changes)**

Thỉnh thoảng, chúng ta có thể thực hiện một thay đổi yêu cầu người tiêu dùng của chúng ta cũng phải thay đổi. Chúng ta sẽ thảo luận về cách xử lý điều này sau, nhưng chúng ta muốn chọn công nghệ đảm bảo điều này xảy ra càng hiếm càng tốt. Ví dụ, nếu một microservice thêm các trường mới vào một phần dữ liệu mà nó gửi đi, những người tiêu dùng hiện tại không nên bị ảnh hưởng.

##### **Giữ cho API của bạn không phụ thuộc vào Công nghệ (Keep Your APIs Technology-Agnostic)**

Nếu bạn đã ở trong ngành CNTT hơn 15 phút, bạn không cần tôi phải nói với bạn rằng chúng ta làm việc trong một không gian đang thay đổi nhanh chóng. Điều chắc chắn duy nhất là *sự thay đổi*. Các công cụ, `framework` và ngôn ngữ mới đang ra đời mọi lúc, thực hiện các ý tưởng mới có thể giúp chúng ta làm việc nhanh hơn và hiệu quả hơn. Ngay bây giờ, bạn có thể là một cửa hàng .NET. Nhưng còn một năm nữa, hoặc năm năm nữa thì sao? Điều gì sẽ xảy ra nếu bạn muốn thử nghiệm với một `technology stack` thay thế có thể làm bạn năng suất hơn?

Tôi là một người rất thích giữ các lựa chọn của mình mở, đó là lý do tại sao tôi rất thích microservices. Đó cũng là lý do tại sao tôi nghĩ rằng việc đảm bảo bạn giữ cho các API được sử dụng để giao tiếp giữa các microservice không phụ thuộc vào công nghệ là rất quan trọng. Điều này có nghĩa là tránh công nghệ tích hợp ra lệnh cho chúng ta có thể sử dụng những `technology stack` nào để triển khai các microservice của mình.

##### **Làm cho Dịch vụ của bạn Đơn giản cho Người tiêu dùng (Make Your Service Simple for Consumers)**

Chúng ta muốn làm cho người tiêu dùng dễ dàng sử dụng dịch vụ của chúng ta. Việc có một microservice được cấu trúc đẹp đẽ không có nhiều ý nghĩa nếu chi phí sử dụng nó với tư cách là người tiêu dùng là quá cao! Vì vậy, hãy suy nghĩ về những gì làm cho người tiêu dùng dễ dàng sử dụng dịch vụ mới tuyệt vời của chúng ta. Lý tưởng nhất, chúng ta muốn cho phép khách hàng của mình hoàn toàn tự do trong lựa chọn công nghệ của họ, nhưng mặt khác, việc cung cấp một thư viện máy khách (`client library`) có thể giúp việc áp dụng dễ dàng hơn. Tuy nhiên, thường thì các thư viện như vậy không tương thích với những thứ khác mà chúng ta muốn đạt được. Ví dụ, chúng ta có thể sử dụng các thư viện máy khách để làm cho người tiêu dùng dễ dàng hơn, nhưng điều này có thể đi kèm với cái giá của việc tăng sự ghép nối (`coupling`).

##### **Ẩn chi tiết Triển khai Nội bộ (Hide Internal Implementation Detail)**

Chúng ta không muốn người tiêu dùng của mình bị ràng buộc vào việc triển khai nội bộ của chúng ta. Điều này dẫn đến sự ghép nối tăng lên. Điều này có nghĩa là nếu chúng ta muốn thay đổi một cái gì đó bên trong microservice của mình, chúng ta có thể làm hỏng người tiêu dùng của mình bằng cách yêu cầu họ cũng phải thay đổi. Điều đó làm tăng chi phí thay đổi—kết quả chính xác mà chúng ta đang cố gắng tránh. Nó cũng có nghĩa là chúng ta ít có khả năng muốn thực hiện một thay đổi vì sợ phải nâng cấp người tiêu dùng của mình, điều này có thể dẫn đến nợ kỹ thuật (`technical debt`) tăng lên trong dịch vụ. Vì vậy, bất kỳ công nghệ nào đẩy chúng ta phơi bày chi tiết biểu diễn nội bộ nên được tránh.

#### **Giao tiếp với Khách hàng (Interfacing with Customers)**

Bây giờ chúng ta đã có một vài hướng dẫn có thể giúp chúng ta chọn một công nghệ tốt để sử dụng cho việc tích hợp giữa các dịch vụ, hãy xem xét một số tùy chọn phổ biến nhất hiện có và cố gắng tìm ra cái nào hoạt động tốt nhất cho chúng ta. Để giúp chúng ta suy nghĩ thấu đáo về điều này, hãy chọn một ví dụ thực tế từ MusicCorp.

Việc tạo khách hàng thoạt nhìn có thể được coi là một tập hợp các hoạt động CRUD đơn giản, nhưng đối với hầu hết các hệ thống, nó phức tạp hơn thế. Việc đăng ký một khách hàng mới có thể cần phải khởi động các quy trình bổ sung, như thiết lập thanh toán tài chính hoặc gửi email chào mừng. Và khi chúng ta thay đổi hoặc xóa một khách hàng, các quy trình nghiệp vụ khác cũng có thể được kích hoạt.

Vì vậy, với ý nghĩ đó, chúng ta nên xem xét một số cách khác nhau mà chúng ta có thể muốn làm việc với khách hàng trong hệ thống MusicCorp của mình.

---
#### **Cơ sở dữ liệu Dùng chung (The Shared Database)**

Đến nay, hình thức tích hợp phổ biến nhất mà tôi hoặc bất kỳ đồng nghiệp nào của tôi thấy trong ngành là tích hợp cơ sở dữ liệu (`database - DB`). Trong thế giới này, nếu các dịch vụ khác muốn thông tin từ một dịch vụ, chúng sẽ truy cập vào cơ sở dữ liệu. Và nếu chúng muốn thay đổi nó, chúng cũng truy cập vào cơ sở dữ liệu! Điều này thực sự đơn giản khi bạn mới nghĩ về nó, và có lẽ là hình thức tích hợp nhanh nhất để bắt đầu—điều này có lẽ giải thích sự phổ biến của nó.

Hình 4-1 cho thấy giao diện người dùng đăng ký của chúng ta, tạo khách hàng bằng cách thực hiện các hoạt động SQL trực tiếp trên cơ sở dữ liệu. Nó cũng cho thấy ứng dụng trung tâm cuộc gọi của chúng ta xem và chỉnh sửa dữ liệu khách hàng bằng cách chạy SQL trên cơ sở dữ liệu. Và nhà kho cập nhật thông tin về các đơn đặt hàng của khách hàng bằng cách truy vấn cơ sở dữ liệu. Đây là một mẫu đủ phổ biến, nhưng nó đầy rẫy những khó khăn.

![alt text](<images/Screenshot from 2025-09-29 06-49-15.png>)

**Hình 4-1.** *Sử dụng tích hợp DB để truy cập và thay đổi thông tin khách hàng*

*   **Thứ nhất**, chúng ta đang cho phép các bên ngoài xem và ràng buộc với các chi tiết triển khai nội bộ. Các cấu trúc dữ liệu tôi lưu trữ trong DB là sân chơi chung cho tất cả; chúng được chia sẻ toàn bộ với tất cả các bên khác có quyền truy cập vào cơ sở dữ liệu. Nếu tôi quyết định thay đổi `schema` của mình để biểu diễn dữ liệu của mình tốt hơn, hoặc làm cho hệ thống của tôi dễ bảo trì hơn, tôi có thể làm hỏng người tiêu dùng của mình. DB thực chất là một API rất lớn, được chia sẻ, cũng khá mong manh. Nếu tôi muốn thay đổi logic liên quan đến, chẳng hạn, cách bộ phận trợ giúp quản lý khách hàng và điều này yêu cầu một thay đổi đối với cơ sở dữ liệu, tôi phải hết sức cẩn thận để không làm hỏng các phần của `schema` được sử dụng bởi các dịch vụ khác. Tình huống này thường dẫn đến yêu cầu một lượng lớn kiểm thử hồi quy (`regression testing`).

*   **Thứ hai**, người tiêu dùng của tôi bị ràng buộc vào một lựa chọn công nghệ cụ thể. Có lẽ bây giờ việc lưu trữ khách hàng trong một cơ sở dữ liệu quan hệ là hợp lý, vì vậy người tiêu dùng của tôi sử dụng một `driver` phù hợp (có thể là cụ thể cho DB) để nói chuyện với nó. Điều gì sẽ xảy ra nếu theo thời gian chúng ta nhận ra rằng chúng ta sẽ tốt hơn nếu lưu trữ dữ liệu trong một cơ sở dữ liệu phi quan hệ? Nó có thể đưa ra quyết định đó không? Vì vậy, người tiêu dùng bị ràng buộc chặt chẽ với việc triển khai của dịch vụ khách hàng. Như chúng ta đã thảo luận trước đó, chúng ta thực sự muốn đảm bảo rằng chi tiết triển khai được ẩn khỏi người tiêu dùng để cho phép dịch vụ của chúng ta có một mức độ tự chủ về cách nó thay đổi nội bộ của mình theo thời gian. Tạm biệt, `loose coupling`.

Cuối cùng, hãy suy nghĩ về hành vi trong giây lát. Sẽ có logic liên quan đến cách một khách hàng được thay đổi. Logic đó ở đâu? Nếu người tiêu dùng đang thao tác trực tiếp với DB, thì họ phải sở hữu logic liên quan. Logic để thực hiện các loại thao tác tương tự đối với một khách hàng bây giờ có thể được lan truyền giữa nhiều người tiêu dùng. Nếu nhà kho, giao diện người dùng đăng ký, và giao diện người dùng trung tâm cuộc gọi đều cần chỉnh sửa thông tin khách hàng, tôi cần phải sửa một lỗi hoặc thay đổi hành vi ở ba nơi khác nhau, và cũng triển khai những thay đổi đó. Tạm biệt, `cohesion`.

Hãy nhớ khi chúng ta nói về các nguyên tắc cốt lõi đằng sau các microservice tốt? Gắn kết mạnh mẽ và ghép nối lỏng lẻo—với tích hợp cơ sở dữ liệu, chúng ta mất cả hai. Tích hợp cơ sở dữ liệu giúp các dịch vụ chia sẻ dữ liệu dễ dàng, nhưng không làm gì về việc chia sẻ hành vi. Biểu diễn nội bộ của chúng ta được phơi bày qua dây (`over the wire`) cho người tiêu dùng của chúng ta, và có thể rất khó để tránh thực hiện các thay đổi gây đổ vỡ, điều này chắc chắn dẫn đến nỗi sợ hãi bất kỳ thay đổi nào. **Tránh bằng mọi giá (gần như)**.

Trong phần còn lại của chương, chúng ta sẽ khám phá các kiểu tích hợp khác nhau liên quan đến các dịch vụ hợp tác, mà bản thân chúng ẩn các biểu diễn nội bộ của riêng mình.

#### **Đồng bộ so với Bất đồng bộ (Synchronous Versus Asynchronous)**

Trước khi chúng ta đi sâu vào chi tiết của các lựa chọn công nghệ khác nhau, chúng ta nên thảo luận về một trong những quyết định quan trọng nhất mà chúng ta có thể đưa ra về cách các dịch vụ hợp tác. Liệu giao tiếp nên là **đồng bộ (synchronous)** hay **bất đồng bộ (asynchronous)**? Lựa chọn cơ bản này chắc chắn sẽ hướng chúng ta đến những chi tiết triển khai nhất định.

Với giao tiếp **đồng bộ**, một cuộc gọi được thực hiện đến một máy chủ từ xa, và nó sẽ **chặn (blocks)** cho đến khi hoạt động hoàn tất. Với giao tiếp **bất đồng bộ**, người gọi không đợi hoạt động hoàn tất trước khi trả về, và thậm chí có thể không quan tâm liệu hoạt động có hoàn tất hay không.

Giao tiếp **đồng bộ** có thể dễ dàng hơn để lý giải. Chúng ta biết khi nào mọi thứ đã hoàn thành thành công hay chưa. Giao tiếp **bất đồng bộ** có thể rất hữu ích cho các công việc chạy dài (`long-running jobs`), nơi việc giữ một kết nối mở trong một khoảng thời gian dài giữa máy khách và máy chủ là không thực tế. Nó cũng hoạt động rất tốt khi bạn cần độ trễ thấp, nơi việc chặn một cuộc gọi trong khi chờ kết quả có thể làm chậm mọi thứ. Do bản chất của mạng di động và các thiết bị, việc gửi yêu cầu và giả định mọi thứ đã hoạt động (trừ khi được thông báo ngược lại) có thể đảm bảo rằng giao diện người dùng (UI) vẫn phản hồi ngay cả khi mạng có độ trễ cao. Mặt khác, công nghệ để xử lý giao tiếp bất đồng bộ có thể phức tạp hơn một chút, như chúng ta sẽ thảo luận ngay sau đây.

Hai chế độ giao tiếp khác nhau này có thể cho phép hai kiểu hợp tác mang tính thành ngữ (`idiomatic styles`) khác nhau: **yêu cầu/phản hồi (request/response)** hoặc **dựa trên sự kiện (event-based)**. Với `request/response`, một máy khách khởi tạo một yêu cầu và đợi phản hồi. Mô hình này rõ ràng phù hợp tốt với giao tiếp đồng bộ, nhưng cũng có thể hoạt động cho giao tiếp bất đồng bộ. Tôi có thể khởi động một hoạt động và đăng ký một `callback`, yêu cầu máy chủ cho tôi biết khi nào hoạt động của tôi đã hoàn thành.

Với một sự hợp tác **dựa trên sự kiện**, chúng ta đảo ngược mọi thứ. Thay vì một máy khách khởi tạo các yêu cầu yêu cầu mọi thứ được thực hiện, nó thay vào đó nói *điều này đã xảy ra* và mong đợi các bên khác biết phải làm gì. Chúng ta không bao giờ nói cho ai khác phải làm gì. Các hệ thống dựa trên sự kiện về bản chất là bất đồng bộ. Sự thông minh được phân phối đều hơn—nghĩa là, logic nghiệp vụ không được tập trung vào các bộ não cốt lõi, mà thay vào đó được đẩy ra đều hơn cho các cộng tác viên khác nhau. Sự hợp tác dựa trên sự kiện cũng được **tách rời cao (highly decoupled)**. Máy khách phát ra một sự kiện không có cách nào biết ai hoặc cái gì sẽ phản ứng với nó, điều này cũng có nghĩa là bạn có thể thêm các người đăng ký mới vào các sự kiện này mà máy khách không bao giờ cần biết.

Vậy có bất kỳ động lực nào khác có thể đẩy chúng ta chọn một kiểu này thay vì kiểu khác không? Một yếu tố quan trọng cần xem xét là các kiểu này phù hợp như thế nào để giải quyết một vấn đề thường phức tạp: làm thế nào chúng ta xử lý các quy trình kéo dài qua các ranh giới dịch vụ và có thể chạy trong thời gian dài?

#### **Điều phối so với Vũ đạo (Orchestration Versus Choreography)**

Khi chúng ta bắt đầu mô hình hóa logic ngày càng phức tạp, chúng ta phải đối mặt với vấn đề quản lý các quy trình nghiệp vụ kéo dài qua ranh giới của các dịch vụ riêng lẻ. Và với microservices, chúng ta sẽ đạt đến giới hạn này sớm hơn bình thường. Hãy lấy một ví dụ từ MusicCorp, và xem điều gì xảy ra khi chúng ta tạo một khách hàng:

1.  Một bản ghi mới được tạo trong ngân hàng điểm khách hàng thân thiết cho khách hàng.
2.  Hệ thống bưu chính của chúng ta gửi đi một gói chào mừng.
3.  Chúng ta gửi một email chào mừng đến khách hàng.

Điều này rất dễ mô hình hóa về mặt khái niệm như một lưu đồ, như chúng ta làm trong Hình 4-2.

![alt text](<images/Screenshot from 2025-09-29 06-49-25.png>)

**Hình 4-2.** *Quy trình tạo một khách hàng mới*

Khi nói đến việc thực sự triển khai luồng này, có hai kiểu kiến trúc chúng ta có thể theo. Với **điều phối (orchestration)**, chúng ta dựa vào một bộ não trung tâm để hướng dẫn và điều khiển quy trình, giống như nhạc trưởng trong một dàn nhạc. Với **vũ đạo (choreography)**, chúng ta thông báo cho mỗi bộ phận của hệ thống về công việc của nó, và để nó tự giải quyết các chi tiết, giống như các vũ công đều tìm đường và phản ứng với những người khác xung quanh họ trong một vở ballet.

Hãy suy nghĩ về một giải pháp điều phối sẽ trông như thế nào cho luồng này. Ở đây, có lẽ điều đơn giản nhất cần làm là để dịch vụ khách hàng của chúng ta hoạt động như bộ não trung tâm. Khi tạo, nó nói chuyện với ngân hàng điểm khách hàng thân thiết, dịch vụ email, và dịch vụ bưu chính như chúng ta thấy trong Hình 4-3, thông qua một loạt các cuộc gọi `request/response`. Bản thân dịch vụ khách hàng sau đó có thể theo dõi khách hàng đang ở đâu trong quy trình này. Nó có thể kiểm tra xem tài khoản của khách hàng đã được thiết lập chưa, hoặc email đã được gửi chưa, hoặc bưu phẩm đã được giao chưa. Chúng ta có thể lấy lưu đồ trong Hình 4-2 và mô hình hóa nó trực tiếp vào mã. Chúng ta thậm chí có thể sử dụng các công cụ thực hiện điều này cho chúng ta, có lẽ bằng cách sử dụng một `rules engine` phù hợp. Các công cụ thương mại tồn tại cho mục đích này dưới dạng phần mềm mô hình hóa quy trình nghiệp vụ. Giả sử chúng ta sử dụng `request/response` đồng bộ, chúng ta thậm chí có thể biết liệu mỗi giai đoạn đã hoạt động hay chưa.

![alt text](<images/Screenshot from 2025-09-29 06-49-36.png>)

**Hình 4-3.** *Xử lý việc tạo khách hàng thông qua điều phối*

Nhược điểm của phương pháp điều phối này là dịch vụ khách hàng có thể trở thành một cơ quan quản lý trung tâm quá mức. Nó có thể trở thành trung tâm ở giữa một mạng lưới, và một điểm trung tâm nơi logic bắt đầu tồn tại. Tôi đã thấy cách tiếp cận này dẫn đến một số lượng nhỏ các dịch vụ "thần thánh" thông minh ra lệnh cho các dịch vụ `anemic` dựa trên CRUD phải làm gì.

Với một cách tiếp cận **vũ đạo**, chúng ta thay vào đó có thể chỉ cần để dịch vụ khách hàng phát ra một sự kiện một cách bất đồng bộ, nói rằng *Khách hàng đã được tạo*. Dịch vụ email, dịch vụ bưu chính, và ngân hàng điểm khách hàng thân thiết sau đó chỉ cần đăng ký các sự kiện này và phản ứng tương ứng, như trong Hình 4-4. Cách tiếp cận này được tách rời một cách đáng kể hơn. Nếu một dịch vụ nào đó khác cần phản ứng với việc tạo ra một khách hàng, nó chỉ cần đăng ký các sự kiện và thực hiện công việc của mình khi cần thiết. Nhược điểm là cái nhìn rõ ràng về quy trình nghiệp vụ mà chúng ta thấy trong Hình 4-2 bây giờ chỉ được phản ánh một cách ngầm định trong hệ thống của chúng ta.

![alt text](<images/Screenshot from 2025-09-29 06-49-44.png>)

**Hình 4-4.** *Xử lý việc tạo khách hàng thông qua vũ đạo*

Điều này có nghĩa là cần thêm công việc để đảm bảo rằng bạn có thể giám sát và theo dõi rằng những điều đúng đắn đã xảy ra. Ví dụ, bạn có biết nếu ngân hàng điểm khách hàng thân thiết có một lỗi và vì lý do nào đó đã không thiết lập đúng tài khoản không? Một cách tiếp cận mà tôi thích để đối phó với điều này là xây dựng một hệ thống giám sát khớp một cách rõ ràng với cái nhìn về quy trình nghiệp vụ trong Hình 4-2, nhưng sau đó theo dõi những gì mỗi dịch vụ làm như các thực thể độc lập, cho phép bạn thấy các ngoại lệ kỳ lạ được ánh xạ lên luồng quy trình rõ ràng hơn. Lưu đồ chúng ta đã thấy trước đó không phải là lực lượng thúc đẩy, mà chỉ là một lăng kính qua đó chúng ta có thể thấy hệ thống đang hoạt động như thế nào.

Nói chung, tôi đã thấy rằng các hệ thống có xu hướng nghiêng về phương pháp vũ đạo hơn sẽ được ghép nối lỏng lẻo hơn, và linh hoạt hơn và dễ thay đổi hơn. Tuy nhiên, bạn cần phải làm thêm công việc để giám sát và theo dõi các quy trình qua các ranh giới hệ thống. Tôi đã thấy hầu hết các triển khai điều phối nặng nề đều cực kỳ mong manh, với chi phí thay đổi cao hơn. Với ý nghĩ đó, tôi rất thích nhắm đến một hệ thống vũ đạo, nơi mỗi dịch vụ đủ thông minh để hiểu vai trò của mình trong toàn bộ vũ điệu.

Có khá nhiều yếu tố để phân tích ở đây. Các cuộc gọi đồng bộ đơn giản hơn, và chúng ta biết được liệu mọi thứ có hoạt động ngay lập tức hay không. Nếu chúng ta thích ngữ nghĩa của `request/response` nhưng đang đối phó với các quy trình sống lâu hơn, chúng ta có thể chỉ cần khởi tạo các yêu cầu bất đồng bộ và đợi các `callback`. Mặt khác, sự hợp tác sự kiện bất đồng bộ giúp chúng ta áp dụng một cách tiếp cận vũ đạo, có thể mang lại các dịch vụ được tách rời một cách đáng kể hơn—một điều chúng ta muốn phấn đấu để đảm bảo các dịch vụ của mình có thể được phát hành một cách độc lập.

Tất nhiên, chúng ta có thể tự do kết hợp. Một số công nghệ sẽ phù hợp tự nhiên hơn với một kiểu này hoặc kiểu khác. Tuy nhiên, chúng ta cần phải đánh giá một số chi tiết triển khai kỹ thuật khác nhau sẽ giúp chúng ta đưa ra quyết định đúng đắn hơn.

Để bắt đầu, hãy xem xét hai công nghệ phù hợp tốt khi chúng ta đang xem xét `request/response`: **gọi thủ tục từ xa (Remote Procedure Call - RPC)** và **Truyền trạng thái Đại diện (REpresentational State Transfer - REST)**.

#### **Gọi thủ tục từ xa (Remote Procedure Calls - RPC)**

**Gọi thủ tục từ xa (Remote procedure call - RPC)** đề cập đến kỹ thuật thực hiện một cuộc gọi cục bộ và để nó thực thi trên một dịch vụ từ xa ở đâu đó. Có một số loại công nghệ RPC khác nhau. Một số công nghệ này dựa vào việc có một định nghĩa giao diện (`interface definition`) (như `SOAP`, `Thrift`, `protocol buffers`). Việc sử dụng một định nghĩa giao diện riêng biệt có thể giúp dễ dàng tạo ra các `stub` máy khách và máy chủ cho các `technology stack` khác nhau, ví dụ, tôi có thể có một máy chủ Java phơi bày một giao diện `SOAP`, và một máy khách .NET được tạo ra từ định nghĩa Ngôn ngữ Định nghĩa Dịch vụ Web (`Web Service Definition Language - WSDL`) của giao diện. Các công nghệ khác, như Java `RMI`, yêu cầu một sự ghép nối chặt chẽ hơn giữa máy khách và máy chủ, yêu cầu cả hai phải sử dụng cùng một công nghệ nền tảng nhưng tránh được nhu cầu về một định nghĩa giao diện chung. Tuy nhiên, tất cả các công nghệ này đều có cùng một đặc điểm cốt lõi là chúng làm cho một cuộc gọi cục bộ trông giống như một cuộc gọi từ xa.

Nhiều công nghệ trong số này có bản chất nhị phân (`binary`), như Java `RMI`, `Thrift`, hoặc `protocol buffers`, trong khi `SOAP` sử dụng `XML` cho các định dạng thông điệp của nó. Một số triển khai được gắn với một giao thức mạng cụ thể (như `SOAP`, mà danh nghĩa sử dụng `HTTP`), trong khi những triển khai khác có thể cho phép bạn sử dụng các loại giao thức mạng khác nhau, mà bản thân chúng có thể cung cấp các tính năng bổ sung. Ví dụ, `TCP` cung cấp các đảm bảo về việc gửi, trong khi `UDP` thì không nhưng có chi phí thấp hơn nhiều. Điều này có thể cho phép bạn sử dụng công nghệ mạng khác nhau cho các trường hợp sử dụng khác nhau.

Những triển khai RPC cho phép bạn tạo ra các `stub` máy khách và máy chủ giúp bạn bắt đầu rất, rất nhanh. Tôi có thể gửi nội dung qua một ranh giới mạng ngay lập tức. Đây thường là một trong những điểm bán hàng chính của RPC: sự dễ sử dụng của nó. Thực tế là tôi chỉ có thể thực hiện một cuộc gọi phương thức bình thường và về mặt lý thuyết bỏ qua phần còn lại là một lợi ích to lớn.

Tuy nhiên, một số triển khai RPC lại đi kèm với một số nhược điểm có thể gây ra vấn đề. Những vấn đề này không phải lúc nào cũng rõ ràng ngay từ đầu, nhưng tuy nhiên chúng có thể đủ nghiêm trọng để vượt qua những lợi ích của việc dễ dàng thiết lập và chạy ban đầu.

##### **Ghép nối Công nghệ (Technology Coupling)**

Một số cơ chế RPC, như Java `RMI`, bị ràng buộc chặt chẽ với một nền tảng cụ thể, điều này có thể hạn chế công nghệ nào có thể được sử dụng ở máy khách và máy chủ. `Thrift` và `protocol buffers` có một lượng hỗ trợ ấn tượng cho các ngôn ngữ thay thế, điều này có thể giảm bớt nhược điểm này phần nào, nhưng hãy lưu ý rằng đôi khi công nghệ RPC đi kèm với các hạn chế về khả năng tương tác.

Theo một cách nào đó, sự ghép nối công nghệ này có thể là một hình thức phơi bày các chi tiết triển khai kỹ thuật nội bộ. Ví dụ, việc sử dụng `RMI` không chỉ ràng buộc máy khách với JVM, mà cả máy chủ cũng vậy.

##### **Cuộc gọi Cục bộ không giống như Cuộc gọi Từ xa (Local Calls Are Not Like Remote Calls)**

Ý tưởng cốt lõi của RPC là che giấu sự phức tạp của một cuộc gọi từ xa. Tuy nhiên, nhiều triển khai RPC lại che giấu quá nhiều. Động lực trong một số hình thức RPC để làm cho các cuộc gọi phương thức từ xa trông giống như các cuộc gọi phương thức cục bộ che giấu thực tế rằng hai thứ này rất khác nhau. Tôi có thể thực hiện một số lượng lớn các cuộc gọi cục bộ, trong tiến trình mà không lo lắng quá nhiều về hiệu suất. Tuy nhiên, với RPC, chi phí của việc tuần tự hóa (`marshalling`) và giải tuần tự hóa (`unmarshalling`) `payload` có thể là đáng kể, chưa kể đến thời gian cần thiết để gửi mọi thứ qua mạng. Điều này có nghĩa là bạn cần suy nghĩ khác về thiết kế API cho các giao diện từ xa so với các giao diện cục bộ. Chỉ cần lấy một API cục bộ và cố gắng biến nó thành một ranh giới dịch vụ mà không suy nghĩ thêm gì có khả năng đưa bạn vào rắc rối. Trong một số ví dụ tồi tệ nhất, các nhà phát triển có thể đang sử dụng các cuộc gọi từ xa mà không biết nếu sự trừu tượng hóa quá mờ đục.

Bạn cần suy nghĩ về chính mạng lưới. Nổi tiếng, điều đầu tiên trong những ngụy biện của điện toán phân tán là **"Mạng là đáng tin cậy"**. Mạng không đáng tin cậy. Chúng có thể và sẽ bị lỗi, ngay cả khi máy khách và máy chủ bạn đang nói chuyện đều ổn. Chúng có thể lỗi nhanh, chúng có thể lỗi chậm, và chúng thậm chí có thể làm hỏng các gói tin của bạn. Bạn nên giả định rằng mạng của bạn bị hoành hành bởi các thực thể độc ác sẵn sàng trút giận lên chúng bất cứ lúc nào. Do đó, các chế độ lỗi bạn có thể mong đợi là khác nhau. Một lỗi có thể được gây ra bởi máy chủ từ xa trả về một lỗi, hoặc bởi bạn thực hiện một cuộc gọi tồi. Bạn có thể phân biệt được không, và nếu có, bạn có thể làm gì với nó? Và bạn làm gì khi máy chủ từ xa chỉ bắt đầu phản hồi chậm? Chúng ta sẽ đề cập đến chủ đề này khi nói về khả năng phục hồi trong **Chương 11**.

##### **Tính mong manh (Brittleness)**

Một số triển khai phổ biến nhất của RPC có thể dẫn đến một số hình thức mong manh khó chịu, `RMI` của Java là một ví dụ rất tốt. Hãy xem xét một giao diện Java rất đơn giản mà chúng ta đã quyết định biến thành một API từ xa cho dịch vụ khách hàng của mình. Ví dụ 4-1 khai báo các phương thức chúng ta sẽ phơi bày từ xa. Java `RMI` sau đó tạo ra các `stub` máy khách và máy chủ cho phương thức của chúng ta.

**Ví dụ 4-1.** *Định nghĩa một điểm cuối dịch vụ bằng Java RMI*```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface CustomerRemote extends Remote {
    public Customer findCustomer(String id) throws RemoteException;
    public Customer createCustomer(String firstname, String surname, String emailAddress)
        throws RemoteException;
}
```

Trong giao diện này, `findCustomer` lấy tên, họ, và địa chỉ email. Điều gì xảy ra nếu chúng ta quyết định cho phép đối tượng `Customer` cũng được tạo chỉ với một địa chỉ email? Chúng ta có thể thêm một phương thức mới tại thời điểm này khá dễ dàng, như sau:

```java
...
public Customer createCustomer(String emailAddress) throws RemoteException;
...
```

Vấn đề là bây giờ chúng ta cần phải tạo lại các `stub` máy khách. Các máy khách muốn sử dụng phương thức mới cần các `stub` mới, và tùy thuộc vào bản chất của các thay đổi đối với đặc tả, những người tiêu dùng không cần phương thức mới cũng có thể cần phải nâng cấp `stub` của họ. Tất nhiên, điều này có thể quản lý được, nhưng đến một mức độ nào đó. Thực tế là những thay đổi như thế này khá phổ biến. Các điểm cuối RPC thường kết thúc với một số lượng lớn các phương thức cho các cách tạo hoặc tương tác với các đối tượng khác nhau. Điều này một phần là do thực tế là chúng ta vẫn đang nghĩ về những cuộc gọi từ xa này như những cuộc gọi cục bộ.

Tuy nhiên, có một loại mong manh khác. Hãy xem đối tượng `Customer` của chúng ta trông như thế nào:
```java
public class Customer implements Serializable {
    private String firstName;
    private String surname;
    private String emailAddress;
    private String age;
}
```

Bây giờ, điều gì sẽ xảy ra nếu hóa ra mặc dù chúng ta phơi bày trường `age` trong các đối tượng `Customer` của mình, nhưng không có người tiêu dùng nào của chúng ta từng sử dụng nó? Chúng ta quyết định muốn loại bỏ trường này. Nhưng nếu triển khai máy chủ loại bỏ `age` khỏi định nghĩa của loại này, và chúng ta không làm điều tương tự với tất cả những người tiêu dùng, thì ngay cả khi họ không bao giờ sử dụng trường đó, mã liên quan đến việc giải tuần tự hóa đối tượng `Customer` ở phía người tiêu dùng sẽ bị hỏng. Để triển khai thay đổi này, tôi sẽ phải triển khai cả một máy chủ và các máy khách mới cùng một lúc. Đây là một thách thức chính với bất kỳ cơ chế RPC nào thúc đẩy việc sử dụng tạo `stub` nhị phân: bạn không thể tách biệt việc triển khai máy khách và máy chủ. Nếu bạn sử dụng công nghệ này, các bản phát hành đồng bộ (`lock-step releases`) có thể ở trong tương lai của bạn.

Các vấn đề tương tự xảy ra nếu tôi muốn tái cấu trúc đối tượng `Customer` ngay cả khi tôi không xóa các trường—ví dụ, nếu tôi muốn đóng gói `firstName` và `surname` vào một loại đặt tên mới để dễ quản lý hơn. Tất nhiên, tôi có thể khắc phục điều này bằng cách truyền các loại từ điển làm tham số của các cuộc gọi của mình, nhưng tại thời điểm đó, tôi mất nhiều lợi ích của các `stub` được tạo ra vì tôi vẫn phải khớp và trích xuất các trường tôi muốn một cách thủ công.

Trong thực tế, các đối tượng được sử dụng như một phần của tuần tự hóa nhị phân qua dây có thể được coi là các loại **chỉ mở rộng (expand-only)**. Sự mong manh này dẫn đến các loại được phơi bày qua dây trở thành một khối lượng lớn các trường, một số trong đó không còn được sử dụng nhưng không thể được loại bỏ một cách an toàn.

##### **RPC có tệ không? (Is RPC Terrible?)**

Mặc dù có những thiếu sót của nó, tôi sẽ không đi xa đến mức gọi RPC là tồi tệ. Một số triển khai phổ biến mà tôi đã gặp có thể dẫn đến các loại vấn đề mà tôi đã nêu ở đây. Do những thách thức của việc sử dụng `RMI`, tôi chắc chắn sẽ dành cho công nghệ đó một khoảng cách xa. Nhiều hoạt động rơi vào mô hình dựa trên RPC khá tốt, và các cơ chế hiện đại hơn như `protocol buffers` hoặc `Thrift` giảm thiểu một số tội lỗi của quá khứ bằng cách tránh nhu cầu phát hành đồng bộ mã máy khách và máy chủ.

Chỉ cần nhận thức được một số cạm bẫy tiềm tàng liên quan đến RPC nếu bạn định chọn mô hình này. Đừng trừu tượng hóa các cuộc gọi từ xa của bạn đến mức mạng bị ẩn hoàn toàn, và đảm bảo rằng bạn có thể phát triển giao diện máy chủ mà không cần phải yêu cầu nâng cấp đồng bộ cho các máy khách. Việc tìm kiếm sự cân bằng phù hợp cho mã máy khách của bạn là quan trọng, chẳng hạn. Hãy chắc chắn rằng các máy khách của bạn không mù quáng trước thực tế rằng một cuộc gọi mạng sẽ được thực hiện. Các thư viện máy khách thường được sử dụng trong bối cảnh của RPC, và nếu không được cấu trúc đúng, chúng có thể có vấn đề. Chúng ta sẽ nói nhiều hơn về chúng ngay sau đây.

So với tích hợp cơ sở dữ liệu, RPC chắc chắn là một sự cải tiến khi chúng ta nghĩ về các tùy chọn cho sự hợp tác `request/response`. Nhưng có một lựa chọn khác để xem xét.

### **REST**

**Truyền trạng thái Đại diện (REpresentational State Transfer - REST)** là một phong cách kiến trúc được lấy cảm hứng từ Web. Có nhiều nguyên tắc và ràng buộc đằng sau phong cách REST, nhưng chúng ta sẽ tập trung vào những nguyên tắc thực sự giúp ích khi chúng ta đối mặt với những thách thức tích hợp trong thế giới microservices, và khi chúng ta đang tìm kiếm một phong cách thay thế cho RPC cho các giao diện dịch vụ của mình.

Quan trọng nhất là khái niệm về **tài nguyên (resources)**. Bạn có thể nghĩ về một tài nguyên như một thứ mà chính dịch vụ đó biết đến, chẳng hạn như một `Customer`. Máy chủ tạo ra các **đại diện (representations)** khác nhau của `Customer` này theo yêu cầu. Cách một tài nguyên được hiển thị bên ngoài hoàn toàn được tách rời khỏi cách nó được lưu trữ nội bộ. Một máy khách có thể yêu cầu một đại diện JSON của một `Customer`, ví dụ, ngay cả khi nó được lưu trữ ở một định dạng hoàn toàn khác. Một khi máy khách có một đại diện của `Customer` này, nó có thể đưa ra các yêu cầu để thay đổi nó, và máy chủ có thể tuân thủ hoặc không.

Có nhiều phong cách REST khác nhau, và tôi chỉ đề cập ngắn gọn về chúng ở đây. Tôi thực sự khuyên bạn nên xem xét **Mô hình Trưởng thành Richardson (Richardson Maturity Model)**, nơi các phong cách REST khác nhau được so sánh.

Bản thân REST không thực sự nói về các giao thức cơ bản, mặc dù nó được sử dụng phổ biến nhất trên `HTTP`. Tôi đã thấy các triển khai REST sử dụng các giao thức rất khác nhau trước đây, chẳng hạn như nối tiếp hoặc USB, mặc dù điều này có thể đòi hỏi rất nhiều công việc. Một số tính năng mà `HTTP` cung cấp cho chúng ta như một phần của đặc tả, chẳng hạn như các động từ (`verbs`), làm cho việc triển khai REST trên `HTTP` dễ dàng hơn, trong khi với các giao thức khác, bạn sẽ phải tự xử lý các tính năng này.

##### **REST và HTTP**

Bản thân `HTTP` định nghĩa một số khả năng hữu ích hoạt động rất tốt với phong cách REST. Ví dụ, các động từ `HTTP` (ví dụ: `GET`, `POST`, và `PUT`) đã có những ý nghĩa được hiểu rõ trong đặc tả `HTTP` về cách chúng nên hoạt động với các tài nguyên. Phong cách kiến trúc REST thực sự cho chúng ta biết rằng các phương thức nên hoạt động theo cùng một cách trên tất cả các tài nguyên, và đặc tả `HTTP` tình cờ định nghĩa một loạt các phương thức chúng ta có thể sử dụng. `GET` truy xuất một tài nguyên một cách **idempotent** (bất biến), và `POST` tạo ra một tài nguyên mới. Điều này có nghĩa là chúng ta có thể tránh rất nhiều các phương thức khác nhau như `createCustomer` hoặc `editCustomer`. Thay vào đó, chúng ta có thể chỉ cần `POST` một đại diện của khách hàng để yêu cầu máy chủ tạo một tài nguyên mới, và khởi tạo một yêu cầu `GET` để truy xuất một đại diện của một tài nguyên. Về mặt khái niệm, có một **điểm cuối (endpoint)** dưới dạng một tài nguyên `Customer` trong những trường hợp này, và các hoạt động chúng ta có thể thực hiện trên nó đã được tích hợp sẵn vào giao thức `HTTP`.

`HTTP` cũng mang lại một hệ sinh thái lớn các công cụ và công nghệ hỗ trợ. Chúng ta có thể sử dụng các `proxy` bộ nhớ đệm `HTTP` như Varnish và các bộ cân bằng tải (`load balancers`) như `mod_proxy`, và nhiều công cụ giám sát đã có rất nhiều hỗ trợ cho `HTTP` ngay từ đầu. Những khối xây dựng này cho phép chúng ta xử lý khối lượng lớn lưu lượng `HTTP` và định tuyến chúng một cách thông minh, một cách khá minh bạch. Chúng ta cũng có thể sử dụng tất cả các điều khiển bảo mật có sẵn với `HTTP` để bảo vệ các giao tiếp của mình. Từ xác thực cơ bản đến chứng chỉ máy khách, hệ sinh thái `HTTP` cung cấp cho chúng ta rất nhiều công cụ để làm cho quá trình bảo mật dễ dàng hơn, và chúng ta sẽ khám phá chủ đề đó nhiều hơn trong **Chương 9**. Điều đó nói rằng, để có được những lợi ích này, bạn phải sử dụng `HTTP` tốt. Sử dụng nó một cách tồi tệ, và nó có thể không an toàn và khó mở rộng quy mô như bất kỳ công nghệ nào khác. Sử dụng nó đúng cách, và bạn sẽ nhận được rất nhiều sự giúp đỡ.

Lưu ý rằng `HTTP` cũng có thể được sử dụng để triển khai RPC. Ví dụ, `SOAP` được định tuyến qua `HTTP`, nhưng thật không may lại sử dụng rất ít đặc tả. Các động từ bị bỏ qua, cũng như những thứ đơn giản như mã lỗi `HTTP`. Quá thường xuyên, có vẻ như các tiêu chuẩn và công nghệ hiện có, được hiểu rõ bị bỏ qua để ủng hộ các tiêu chuẩn mới chỉ có thể được triển khai bằng công nghệ hoàn toàn mới—được cung cấp một cách thuận tiện bởi chính các công ty giúp thiết kế các tiêu chuẩn mới ngay từ đầu!

##### **Hypermedia như là Động cơ của Trạng thái Ứng dụng (Hypermedia As the Engine of Application State)**

Một nguyên tắc khác được giới thiệu trong REST có thể giúp chúng ta tránh sự ghép nối giữa máy khách và máy chủ là khái niệm về **hypermedia như là động cơ của trạng thái ứng dụng** (thường được viết tắt là `HATEOAS`, và đúng là nó cần một chữ viết tắt). Đây là một cách diễn đạt khá dày đặc và một khái niệm khá thú vị, vì vậy hãy cùng phân tích nó một chút.

**Hypermedia** là một khái niệm trong đó một phần nội dung chứa các liên kết đến các phần nội dung khác ở nhiều định dạng khác nhau (ví dụ: văn bản, hình ảnh, âm thanh). Điều này應該 quen thuộc với bạn, vì đó là những gì một trang web trung bình làm: bạn theo các liên kết, là một dạng của các điều khiển hypermedia, để xem nội dung liên quan. Ý tưởng đằng sau `HATEOAS` là các máy khách nên thực hiện các tương tác (có khả năng dẫn đến các chuyển đổi trạng thái) với máy chủ thông qua các liên kết này đến các tài nguyên khác. Nó không cần biết chính xác khách hàng sống ở đâu trên máy chủ bằng cách biết URI nào để truy cập; thay vào đó, máy khách tìm và điều hướng các liên kết để tìm thấy những gì nó cần.

Đây là một khái niệm hơi kỳ lạ, vì vậy trước tiên hãy lùi lại một bước và xem xét cách mọi người tương tác với một trang web, mà chúng ta đã xác định là rất phong phú với các điều khiển hypermedia.

Hãy nghĩ về trang web mua sắm Amazon.com. Vị trí của giỏ hàng đã thay đổi theo thời gian. Đồ họa đã thay đổi. Liên kết đã thay đổi. Nhưng với tư cách là con người, chúng ta đủ thông minh để vẫn thấy một giỏ hàng, biết nó là gì, và tương tác với nó. Chúng ta có một sự hiểu biết về ý nghĩa của một giỏ hàng, ngay cả khi hình thức và điều khiển cơ bản được sử dụng để đại diện cho nó đã thay đổi. Chúng ta biết rằng nếu chúng ta muốn xem giỏ hàng, đây là điều khiển chúng ta muốn tương tác. Đây là lý do tại sao các trang web có thể thay đổi dần dần theo thời gian. Miễn là các hợp đồng ngầm định này giữa khách hàng và trang web vẫn được đáp ứng, các thay đổi không cần phải là các thay đổi gây đổ vỡ.

Với các điều khiển hypermedia, chúng ta đang cố gắng đạt được cùng một mức độ thông minh cho những người tiêu dùng điện tử của mình. Hãy xem xét một điều khiển hypermedia mà chúng ta có thể có cho MusicCorp. Chúng ta đã truy cập một tài nguyên đại diện cho một mục danh mục cho một album nhất định trong Ví dụ 4-2. Cùng với thông tin về album, chúng ta thấy một số điều khiển hypermedia.

**Ví dụ 4-2.** *Các điều khiển hypermedia được sử dụng trên một danh sách album*
```xml
<album>
    <name>Give Blood</name>
    <link rel="/artist" href="/artist/theBrakes" /> <!-- 1 -->
    <description>
        Tuyệt vời, ngắn gọn, tàn bạo, hài hước và ồn ào. Phải mua!
    </description>
    <link rel="/instantpurchase" href="/instantPurchase/1234" /> <!-- 2 -->
</album>
```
1.  Điều khiển hypermedia này cho chúng ta biết nơi để tìm thông tin về nghệ sĩ.
2.  Và nếu chúng ta muốn mua album, bây giờ chúng ta biết phải đi đâu.

Trong tài liệu này, chúng ta có hai điều khiển hypermedia. Máy khách đọc một tài liệu như vậy cần biết rằng một điều khiển có quan hệ (`relation`) là `artist` là nơi nó cần điều hướng để lấy thông tin về nghệ sĩ, và `instantpurchase` là một phần của giao thức được sử dụng để mua album. Máy khách phải hiểu ngữ nghĩa của API giống như cách một con người cần hiểu rằng trên một trang web mua sắm, giỏ hàng là nơi chứa các mặt hàng cần mua.

Với tư cách là một máy khách, tôi không cần biết lược đồ URI nào để truy cập để *mua* album, tôi chỉ cần truy cập tài nguyên, tìm điều khiển mua, và điều hướng đến đó. Điều khiển mua có thể thay đổi vị trí, URI có thể thay đổi, hoặc trang web thậm chí có thể gửi tôi đến một dịch vụ khác hoàn toàn, và với tư cách là một máy khách, tôi sẽ không quan tâm. Điều này mang lại cho chúng ta một lượng lớn sự tách rời giữa máy khách và máy chủ.

Chúng ta được trừu tượng hóa rất nhiều khỏi các chi tiết cơ bản ở đây. Chúng ta có thể thay đổi hoàn toàn việc triển khai cách điều khiển được trình bày miễn là máy khách vẫn có thể tìm thấy một điều khiển phù hợp với sự hiểu biết của nó về giao thức, giống như cách một điều khiển giỏ hàng có thể đi từ một liên kết đơn giản đến một điều khiển JavaScript phức tạp hơn. Chúng ta cũng có thể tự do thêm các điều khiển mới vào tài liệu, có lẽ đại diện cho các chuyển đổi trạng thái mới mà chúng ta có thể thực hiện trên tài nguyên được đề cập. Chúng ta sẽ chỉ làm hỏng người tiêu dùng của mình nếu chúng ta thay đổi cơ bản ngữ nghĩa của một trong các điều khiển để nó hoạt động rất khác, hoặc nếu chúng ta loại bỏ hoàn toàn một điều khiển.

Việc sử dụng các điều khiển này để tách rời máy khách và máy chủ mang lại những lợi ích đáng kể theo thời gian, bù đắp rất nhiều cho sự gia tăng nhỏ về thời gian cần thiết để thiết lập và chạy các giao thức này. Bằng cách theo các liên kết, máy khách có thể khám phá dần API, điều này có thể là một khả năng thực sự hữu ích khi chúng ta triển khai các máy khách mới.

Một trong những nhược điểm là việc điều hướng các điều khiển này có thể khá "lắm lời" (`chatty`), vì máy khách cần phải theo các liên kết để tìm hoạt động nó muốn thực hiện. Cuối cùng, đây là một sự đánh đổi. Tôi sẽ đề nghị bạn bắt đầu bằng việc để các máy khách của bạn điều hướng các điều khiển này trước, sau đó tối ưu hóa sau nếu cần thiết. Hãy nhớ rằng chúng ta có rất nhiều sự giúp đỡ ngay từ đầu bằng cách sử dụng `HTTP`, mà chúng ta đã thảo luận trước đó. Những tệ nạn của việc tối ưu hóa sớm đã được ghi nhận rõ ràng trước đây, vì vậy tôi không cần phải mở rộng về chúng ở đây. Cũng lưu ý rằng rất nhiều trong số các cách tiếp cận này đã được phát triển để tạo ra các hệ thống siêu văn bản phân tán, và không phải tất cả chúng đều phù hợp! Đôi khi bạn sẽ thấy mình chỉ muốn RPC kiểu cũ tốt.

---

Cá nhân tôi, tôi là một người hâm mộ việc sử dụng các liên kết để cho phép người tiêu dùng điều hướng các điểm cuối API. Lợi ích của việc khám phá dần dần API và giảm sự ghép nối có thể rất đáng kể. Tuy nhiên, rõ ràng là không phải ai cũng bị thuyết phục, vì tôi không thấy nó được sử dụng nhiều như tôi mong muốn. Tôi nghĩ một phần lớn của điều này là có một số công việc ban đầu cần thiết, nhưng phần thưởng thường đến sau.

##### **JSON, XML, hay Cái gì đó khác? (JSON, XML, or Something Else?)**

Việc sử dụng các định dạng văn bản tiêu chuẩn mang lại cho máy khách rất nhiều sự linh hoạt về cách họ tiêu thụ tài nguyên, và REST qua `HTTP` cho phép chúng ta sử dụng nhiều định dạng khác nhau. Các ví dụ tôi đã đưa ra cho đến nay đều sử dụng `XML`, nhưng ở giai đoạn này, `JSON` là một loại nội dung phổ biến hơn nhiều cho các dịch vụ hoạt động trên `HTTP`.

Thực tế là `JSON` là một định dạng đơn giản hơn nhiều có nghĩa là việc tiêu thụ cũng dễ dàng hơn. Một số người ủng hộ cũng trích dẫn sự nhỏ gọn tương đối của nó khi so sánh với `XML` như một yếu tố chiến thắng khác, mặc dù đây không phải lúc nào cũng là một vấn đề trong thế giới thực.

Tuy nhiên, `JSON` cũng có một số nhược điểm. `XML` định nghĩa điều khiển `link` mà chúng ta đã sử dụng trước đó như một điều khiển hypermedia. Tiêu chuẩn `JSON` không định nghĩa bất cứ điều gì tương tự, vì vậy các kiểu tự chế thường được sử dụng để nhồi nhét khái niệm này vào. **Ngôn ngữ Ứng dụng Siêu văn bản (Hypertext Application Language - HAL)** cố gắng khắc phục điều này bằng cách định nghĩa một số tiêu chuẩn chung cho việc tạo siêu liên kết cho `JSON` (và cả `XML` nữa, mặc dù có thể cho rằng `XML` ít cần sự giúp đỡ hơn). Nếu bạn tuân theo tiêu chuẩn HAL, bạn có thể sử dụng các công cụ như trình duyệt HAL dựa trên web để khám phá các điều khiển hypermedia, điều này có thể làm cho nhiệm vụ tạo một máy khách dễ dàng hơn nhiều.

Tất nhiên, chúng ta không bị giới hạn ở hai định dạng này. Chúng ta có thể gửi gần như bất cứ thứ gì qua `HTTP` nếu chúng ta muốn, ngay cả dữ liệu nhị phân. Tôi đang thấy ngày càng nhiều người chỉ sử dụng `HTML` làm định dạng thay vì `XML`. Đối với một số giao diện, `HTML` có thể đảm nhiệm cả vai trò của giao diện người dùng (UI) và API, mặc dù có những cạm bẫy cần tránh ở đây, vì các tương tác của một con người và một máy tính khá khác nhau! Nhưng đó chắc chắn là một ý tưởng hấp dẫn. Rốt cuộc, có rất nhiều trình phân tích cú pháp `HTML` ngoài kia.

Cá nhân tôi, tuy nhiên, tôi vẫn là một người hâm mộ `XML`. Một số hỗ trợ công cụ tốt hơn. Ví dụ, nếu tôi chỉ muốn trích xuất một số phần nhất định của `payload` (một kỹ thuật chúng ta sẽ thảo luận nhiều hơn trong "Versioning" ở trang 62), tôi có thể sử dụng `XPATH`, là một tiêu chuẩn được hiểu rõ với rất nhiều hỗ trợ công cụ, hoặc thậm chí là các bộ chọn `CSS`, mà nhiều người thấy còn dễ dàng hơn. Với `JSON`, tôi có `JSONPATH`, nhưng điều này không được hỗ trợ rộng rãi. Tôi thấy kỳ lạ khi mọi người chọn `JSON` vì nó đẹp và nhẹ, sau đó cố gắng đẩy các khái niệm vào nó như các điều khiển hypermedia đã tồn tại trong `XML`. Tuy nhiên, tôi chấp nhận rằng tôi có lẽ thuộc về thiểu số ở đây và `JSON` là định dạng được lựa chọn cho hầu hết mọi người!

##### **Coi chừng sự Tiện lợi Quá mức (Beware Too Much Convenience)**

Khi REST trở nên phổ biến hơn, các `framework` giúp chúng ta tạo ra các dịch vụ web RESTFul cũng vậy. Tuy nhiên, một số công cụ này đánh đổi quá nhiều lợi ích ngắn hạn để lấy nỗi đau dài hạn; trong nỗ lực giúp bạn bắt đầu nhanh chóng, chúng có thể khuyến khích một số hành vi xấu. Ví dụ, một số `framework` thực sự làm cho việc chỉ cần lấy các biểu diễn cơ sở dữ liệu của các đối tượng, giải tuần tự hóa chúng thành các đối tượng trong tiến trình, và sau đó phơi bày trực tiếp chúng ra bên ngoài trở nên rất dễ dàng. Tôi nhớ đã thấy điều này được trình diễn tại một hội nghị sử dụng Spring Boot và được trích dẫn như một lợi thế lớn. Sự ghép nối cố hữu mà thiết lập này thúc đẩy trong hầu hết các trường hợp sẽ gây ra nhiều đau đớn hơn so với nỗ lực cần thiết để tách rời các khái niệm này một cách đúng đắn.

Có một vấn đề chung hơn đang diễn ra ở đây. Cách chúng ta quyết định lưu trữ dữ liệu của mình, và cách chúng ta phơi bày nó cho người tiêu dùng, có thể dễ dàng chi phối suy nghĩ của chúng ta. Một mẫu hình mà tôi đã thấy được một trong các đội của chúng tôi sử dụng hiệu quả là trì hoãn việc triển khai lưu trữ bền vững thích hợp cho microservice, cho đến khi giao diện đã đủ ổn định. Trong một giai đoạn tạm thời, các thực thể chỉ được lưu trữ trong một tệp trên đĩa cục bộ, điều này rõ ràng không phải là một giải pháp lâu dài phù hợp. Điều này đảm bảo rằng cách người tiêu dùng muốn sử dụng dịch vụ đã thúc đẩy các quyết định thiết kế và triển khai. Lý do được đưa ra, đã được chứng minh qua kết quả, là quá dễ dàng để cách chúng ta lưu trữ các thực thể miền trong một kho lưu trữ nền tảng ảnh hưởng công khai đến các mô hình chúng ta gửi qua dây cho các cộng tác viên. Một trong những nhược điểm của cách tiếp cận này là chúng ta đang trì hoãn công việc cần thiết để kết nối `data store` của mình. Tuy nhiên, tôi nghĩ đối với các ranh giới dịch vụ mới, đây là một sự đánh đổi có thể chấp nhận được.

##### **Nhược điểm của REST qua HTTP (Downsides to REST Over HTTP)**

Về mặt dễ tiêu thụ, bạn không thể dễ dàng tạo ra một `stub` máy khách cho giao thức ứng dụng REST qua `HTTP` của mình như bạn có thể với RPC. Chắc chắn, thực tế là `HTTP` đang được sử dụng có nghĩa là bạn có thể tận dụng tất cả các thư viện máy khách `HTTP` tuyệt vời hiện có, nhưng nếu bạn muốn triển khai và sử dụng các điều khiển hypermedia với tư cách là một máy khách, bạn gần như phải tự mình làm. Cá nhân tôi nghĩ rằng các thư viện máy khách có thể làm tốt hơn nhiều ở mặt này so với hiện tại, và chúng chắc chắn đã tốt hơn bây giờ so với trước đây, nhưng tôi đã thấy sự phức tạp gia tăng rõ ràng này dẫn đến việc mọi người quay trở lại việc buôn lậu RPC qua `HTTP` hoặc xây dựng các thư viện máy khách dùng chung. Mã dùng chung giữa máy khách và máy chủ có thể rất nguy hiểm, như chúng ta sẽ thảo luận trong "DRY and the Perils of Code Reuse in a Microservice World" ở trang 59.

Một điểm nhỏ hơn là một số `framework` máy chủ web không thực sự hỗ trợ tốt tất cả các động từ `HTTP`. Điều đó có nghĩa là có thể dễ dàng cho bạn để tạo một trình xử lý cho các yêu cầu `GET` hoặc `POST`, nhưng bạn có thể phải nhảy qua các vòng lửa để làm cho các yêu cầu `PUT` hoặc `DELETE` hoạt động. Các `framework` REST đúng đắn như Jersey không có vấn đề này, và bạn thường có thể giải quyết vấn đề này, nhưng nếu bạn bị khóa vào một số lựa chọn `framework` nhất định, điều này có thể hạn chế phong cách REST bạn có thể sử dụng.

Hiệu suất cũng có thể là một vấn đề. `Payload` của REST qua `HTTP` thực sự có thể nhỏ gọn hơn `SOAP` vì nó hỗ trợ các định dạng thay thế như `JSON` hoặc thậm chí là nhị phân, nhưng nó vẫn sẽ không gọn gàng bằng một giao thức nhị phân như `Thrift`. Chi phí của `HTTP` cho mỗi yêu cầu cũng có thể là một mối quan tâm đối với các yêu cầu có độ trễ thấp.

`HTTP`, mặc dù có thể phù hợp tốt với khối lượng lớn lưu lượng truy cập, nhưng không tốt cho các giao tiếp có độ trễ thấp khi so sánh với các giao thức thay thế được xây dựng trên Giao thức Điều khiển Truyền vận (`Transmission Control Protocol - TCP`) hoặc công nghệ mạng khác. Mặc dù có tên như vậy, ví dụ như `WebSockets`, có rất ít liên quan đến Web. Sau cái bắt tay `HTTP` ban đầu, nó chỉ là một kết nối `TCP` giữa máy khách và máy chủ, nhưng nó có thể là một cách hiệu quả hơn nhiều để bạn truyền dữ liệu cho một trình duyệt. Nếu đây là điều bạn quan tâm, hãy lưu ý rằng bạn không thực sự sử dụng nhiều `HTTP`, chứ đừng nói đến bất cứ điều gì liên quan đến REST.

Đối với các giao tiếp từ máy chủ đến máy chủ, nếu độ trễ cực thấp hoặc kích thước thông điệp nhỏ là quan trọng, giao tiếp `HTTP` nói chung có thể không phải là một ý tưởng tốt. Bạn có thể cần phải chọn các giao thức cơ bản khác nhau, như Giao thức Gói dữ liệu Người dùng (`User Datagram Protocol - UDP`), để đạt được hiệu suất bạn muốn, và nhiều `framework` RPC sẽ khá vui vẻ chạy trên các giao thức mạng khác ngoài `TCP`.

Việc tiêu thụ chính các `payload` đòi hỏi nhiều công việc hơn so với những gì được cung cấp bởi một số triển khai RPC hỗ trợ các cơ chế tuần tự hóa và giải tuần tự hóa tiên tiến. Những thứ này có thể trở thành một điểm ghép nối theo đúng nghĩa của chúng giữa máy khách và máy chủ, vì việc triển khai các trình đọc có khả năng chịu lỗi (`tolerant readers`) là một hoạt động không tầm thường (chúng ta sẽ thảo luận về điều này ngay sau đây), nhưng từ quan điểm của việc bắt đầu và chạy, chúng có thể rất hấp dẫn.

Mặc dù có những nhược điểm này, REST qua `HTTP` là một lựa chọn mặc định hợp lý cho các tương tác từ dịch vụ đến dịch vụ. Nếu bạn muốn biết thêm, tôi khuyên bạn nên đọc cuốn *REST in Practice* (O’Reilly), cuốn sách này đề cập sâu về chủ đề REST qua `HTTP`.

#### **Triển khai Hợp tác Dựa trên Sự kiện Bất đồng bộ (Implementing Asynchronous Event-Based Collaboration)**

Chúng ta đã nói một chút về một số công nghệ có thể giúp chúng ta triển khai các mẫu `request/response`. Còn về giao tiếp bất đồng bộ, dựa trên sự kiện thì sao?

##### **Lựa chọn Công nghệ (Technology Choices)**

Có hai phần chính chúng ta cần xem xét ở đây: một cách để các microservice của chúng ta **phát ra (emit)** các sự kiện, và một cách để người tiêu dùng của chúng ta **tìm ra (find out)** các sự kiện đó đã xảy ra.

Theo truyền thống, các **bộ môi giới thông điệp (message brokers)** như RabbitMQ cố gắng xử lý cả hai vấn đề. Các nhà sản xuất (producers) sử dụng một API để xuất bản một sự kiện đến bộ môi giới. Bộ môi giới xử lý các đăng ký (subscriptions), cho phép người tiêu dùng được thông báo khi một sự kiện đến. Các bộ môi giới này thậm chí có thể xử lý trạng thái của người tiêu dùng, ví dụ bằng cách giúp theo dõi những thông điệp nào họ đã thấy trước đây. Các hệ thống này thường được thiết kế để có khả năng mở rộng và phục hồi, nhưng điều đó không miễn phí. Nó có thể thêm sự phức tạp vào quá trình phát triển, bởi vì đó là một hệ thống khác mà bạn có thể cần phải chạy để phát triển và kiểm thử các dịch vụ của mình. Các máy móc và chuyên môn bổ sung cũng có thể được yêu cầu để giữ cho cơ sở hạ tầng này hoạt động. Nhưng một khi nó hoạt động, nó có thể là một cách cực kỳ hiệu quả để triển khai các kiến trúc hướng sự kiện, ghép nối lỏng lẻo. Nói chung, tôi là một người hâm mộ.

Tuy nhiên, hãy cẩn thận về thế giới của `middleware`, trong đó `message broker` chỉ là một phần nhỏ. Hàng đợi (`queues`) tự thân chúng là những thứ hoàn toàn hợp lý, hữu ích. Tuy nhiên, các nhà cung cấp có xu hướng muốn đóng gói rất nhiều phần mềm cùng với chúng, điều này có thể dẫn đến việc ngày càng nhiều sự thông minh bị đẩy vào `middleware`, được chứng minh bởi những thứ như **Bus Dịch vụ Doanh nghiệp (Enterprise Service Bus - ESB)**. Hãy chắc chắn rằng bạn biết mình đang nhận được gì: hãy giữ cho `middleware` của bạn câm (`dumb`), và giữ sự thông minh ở các điểm cuối (`endpoints`).

Một cách tiếp cận khác là cố gắng sử dụng `HTTP` như một cách để truyền bá các sự kiện. **ATOM** là một đặc tả tuân thủ REST định nghĩa các ngữ nghĩa (trong số những thứ khác) để xuất bản các nguồn cấp dữ liệu (`feeds`) của tài nguyên. Nhiều thư viện máy khách tồn tại cho phép chúng ta tạo và tiêu thụ các nguồn cấp dữ liệu này. Vì vậy, dịch vụ khách hàng của chúng ta có thể chỉ cần xuất bản một sự kiện đến một nguồn cấp dữ liệu như vậy khi dịch vụ khách hàng của chúng ta thay đổi. Người tiêu dùng của chúng ta chỉ cần thăm dò (`poll`) nguồn cấp dữ liệu, tìm kiếm các thay đổi. Một mặt, thực tế là chúng ta có thể tái sử dụng đặc tả ATOM hiện có và bất kỳ thư viện liên quan nào là hữu ích, và chúng ta biết rằng `HTTP` xử lý quy mô rất tốt. Tuy nhiên, `HTTP` không tốt ở độ trễ thấp (nơi một số `message broker` vượt trội), và chúng ta vẫn cần phải đối phó với thực tế là người tiêu dùng cần phải theo dõi những thông điệp nào họ đã thấy và quản lý lịch trình thăm dò của riêng họ.

Tôi đã thấy mọi người dành cả một thời gian dài để triển khai ngày càng nhiều các hành vi mà bạn có được ngay từ đầu với một `message broker` phù hợp để làm cho ATOM hoạt động cho một số trường hợp sử dụng. Ví dụ, mẫu **Người tiêu dùng Cạnh tranh (Competing Consumer)** mô tả một phương pháp theo đó bạn đưa ra nhiều phiên bản worker để cạnh tranh cho các thông điệp, hoạt động tốt cho việc mở rộng quy mô số lượng worker để xử lý một danh sách các công việc độc lập. Tuy nhiên, chúng ta muốn tránh trường hợp hai hoặc nhiều worker thấy cùng một thông điệp, vì chúng ta sẽ kết thúc việc làm cùng một nhiệm vụ nhiều hơn chúng ta cần. Với một `message broker`, một hàng đợi tiêu chuẩn sẽ xử lý điều này. Với ATOM, bây giờ chúng ta cần quản lý trạng thái chia sẻ của riêng mình giữa tất cả các worker để cố gắng giảm khả năng tái tạo nỗ lực.

Nếu bạn đã có một `message broker` tốt, có khả năng phục hồi sẵn có, hãy xem xét sử dụng nó để xử lý việc xuất bản và đăng ký các sự kiện. Nhưng nếu bạn chưa có, hãy xem xét ATOM, nhưng hãy nhận thức được ngụy biện chi phí chìm (`sunk-cost fallacy`). Nếu bạn thấy mình muốn ngày càng nhiều sự hỗ trợ mà một `message broker` cung cấp cho bạn, tại một thời điểm nhất định, bạn có thể muốn thay đổi cách tiếp cận của mình.

Về những gì chúng ta thực sự gửi qua các giao thức bất đồng bộ này, các cân nhắc tương tự cũng áp dụng như với giao tiếp đồng bộ. Nếu bạn hiện đang hài lòng với việc mã hóa các yêu cầu và phản hồi bằng `JSON`, hãy tiếp tục với nó.

##### **Sự phức tạp của Kiến trúc Bất đồng bộ (Complexities of Asynchronous Architectures)**

Một số thứ bất đồng bộ này nghe có vẻ vui, phải không? Các kiến trúc hướng sự kiện dường như dẫn đến các hệ thống được tách rời, có khả năng mở rộng một cách đáng kể hơn. Và chúng có thể. Nhưng những phong cách lập trình này dẫn đến sự gia tăng về sự phức tạp. Đây không chỉ là sự phức tạp cần thiết để quản lý việc xuất bản và đăng ký các thông điệp như chúng ta vừa thảo luận, mà còn ở những vấn đề khác mà chúng ta có thể phải đối mặt. Ví dụ, khi xem xét `request/response` bất đồng bộ chạy dài, chúng ta phải suy nghĩ về những gì phải làm khi phản hồi quay trở lại. Nó có quay trở lại cùng một nút đã khởi tạo yêu cầu không? Nếu có, điều gì sẽ xảy ra nếu nút đó đã tắt? Nếu không, tôi có cần lưu trữ thông tin ở đâu đó để tôi có thể phản ứng tương ứng không? Bất đồng bộ ngắn hạn có thể dễ dàng quản lý hơn nếu bạn có các API phù hợp, nhưng ngay cả như vậy, đó là một cách suy nghĩ khác đối với các lập trình viên đã quen với các cuộc gọi thông điệp đồng bộ trong tiến trình.

Thời gian cho một câu chuyện cảnh báo. Quay trở lại năm 2006, tôi đang làm việc xây dựng một hệ thống định giá cho một ngân hàng. Chúng tôi sẽ xem xét các sự kiện thị trường, và tìm ra những mục nào trong một danh mục đầu tư cần được định giá lại. Một khi chúng tôi xác định danh sách những thứ cần xử lý, chúng tôi đặt tất cả chúng vào một hàng đợi thông điệp. Chúng tôi đang sử dụng một lưới (`grid`) để tạo ra một nhóm các worker định giá, cho phép chúng tôi mở rộng quy mô trang trại định giá lên và xuống theo yêu cầu. Các worker này sử dụng mẫu người tiêu dùng cạnh tranh, mỗi người ngấu nghiến các thông điệp nhanh nhất có thể cho đến khi không còn gì để xử lý.

Hệ thống đã hoạt động, và chúng tôi cảm thấy khá tự mãn. Tuy nhiên, một ngày nọ, ngay sau khi chúng tôi đẩy ra một bản phát hành, chúng tôi đã gặp phải một vấn đề khó chịu. Các worker của chúng tôi liên tục chết. Và chết. Và chết.

Cuối cùng, chúng tôi đã truy ra được vấn đề. Một lỗi đã lẻn vào, theo đó một loại yêu cầu định giá nhất định sẽ khiến một worker bị treo. Chúng tôi đang sử dụng một hàng đợi có giao dịch (`transacted queue`): khi worker chết, khóa của nó trên yêu cầu hết hạn, và yêu cầu định giá được đặt trở lại hàng đợi—chỉ để một worker khác nhặt nó lên và chết. Đây là một ví dụ kinh điển về những gì Martin Fowler gọi là **chuyển đổi dự phòng thảm khốc (catastrophic failover)**.

Ngoài bản thân lỗi, chúng tôi đã không chỉ định giới hạn thử lại tối đa cho công việc trên hàng đợi. Chúng tôi đã sửa bản thân lỗi, và cũng cấu hình một lần thử lại tối đa. Nhưng chúng tôi cũng nhận ra rằng chúng tôi cần một cách để xem, và có khả năng phát lại, những thông điệp xấu này. Cuối cùng, chúng tôi đã phải triển khai một **bệnh viện thông điệp (message hospital)** (hoặc hàng đợi thư chết), nơi các thông điệp được gửi đến nếu chúng thất bại. Chúng tôi cũng tạo ra một giao diện người dùng để xem những thông điệp đó và thử lại chúng nếu cần. Các loại vấn đề này không ngay lập tức rõ ràng nếu bạn chỉ quen thuộc với giao tiếp điểm-đến-điểm đồng bộ.

Sự phức tạp liên quan đến các kiến trúc hướng sự kiện và lập trình bất đồng bộ nói chung khiến tôi tin rằng bạn nên thận trọng trong việc háo hức áp dụng những ý tưởng này. Hãy đảm bảo bạn có hệ thống giám sát tốt, và xem xét mạnh mẽ việc sử dụng các **ID tương quan (correlation IDs)**, cho phép bạn theo dõi các yêu cầu qua các ranh giới tiến trình, như chúng ta sẽ đề cập sâu hơn trong **Chương 8**.

Tôi cũng rất khuyến khích cuốn *Enterprise Integration Patterns* (Addison-Wesley), cuốn sách này chứa nhiều chi tiết hơn về các mẫu lập trình khác nhau mà bạn có thể cần xem xét trong lĩnh vực này.

#### **Dịch vụ như là Máy trạng thái (Services as State Machines)**

Dù bạn chọn trở thành một ninja `REST`, hay gắn bó với một cơ chế dựa trên `RPC` như `SOAP`, khái niệm cốt lõi về dịch vụ như một **máy trạng thái (state machine)** là rất mạnh mẽ. Chúng ta đã nói trước đây (có lẽ đến mức nhàm chán) về việc các dịch vụ của chúng ta được định hình xung quanh các `bounded context`. Microservice khách hàng của chúng ta sở hữu tất cả logic liên quan đến hành vi trong `context` này.

Khi một người tiêu dùng muốn thay đổi một khách hàng, nó sẽ gửi một yêu cầu phù hợp đến dịch vụ khách hàng. Dịch vụ khách hàng, dựa trên logic của nó, sẽ quyết định xem nó có chấp nhận yêu cầu đó hay không. Dịch vụ khách hàng của chúng ta kiểm soát tất cả các sự kiện trong vòng đời liên quan đến chính khách hàng. Chúng ta muốn tránh các dịch vụ `anemic` (còi cọc), ngu ngốc mà không hơn gì các `wrapper` CRUD. Nếu quyết định về những thay đổi nào được phép thực hiện đối với một khách hàng bị rò rỉ ra khỏi chính dịch vụ khách hàng, chúng ta đang mất đi **tính gắn kết (cohesion)**.

Việc có vòng đời của các khái niệm miền nghiệp vụ chính được mô hình hóa một cách rõ ràng như thế này là khá mạnh mẽ. Chúng ta không chỉ có một nơi để đối phó với các xung đột trạng thái (ví dụ, ai đó đang cố gắng cập nhật một khách hàng đã bị xóa), mà chúng ta còn có một nơi để gắn hành vi dựa trên những thay đổi trạng thái đó.

Tôi vẫn nghĩ rằng `REST` qua `HTTP` tạo nên một công nghệ tích hợp hợp lý hơn nhiều so với nhiều công nghệ khác, nhưng bất kể bạn chọn gì, hãy ghi nhớ ý tưởng này.

#### **Mở rộng Phản ứng (Reactive Extensions)**

**Mở rộng Phản ứng (Reactive extensions)**, thường được viết tắt là **Rx**, là một cơ chế để kết hợp kết quả của nhiều cuộc gọi lại với nhau và chạy các hoạt động trên chúng. Bản thân các cuộc gọi có thể là các cuộc gọi chặn hoặc không chặn. Về cốt lõi, Rx đảo ngược các luồng truyền thống. Thay vì yêu cầu một số dữ liệu, sau đó thực hiện các hoạt động trên nó, bạn **quan sát (observe)** kết quả của một hoạt động (hoặc một tập hợp các hoạt động) và **phản ứng (react)** khi có điều gì đó thay đổi. Một số triển khai của Rx cho phép bạn thực hiện các hàm trên những thứ có thể quan sát được này, chẳng hạn như RxJava, cho phép sử dụng các hàm truyền thống như `map` hoặc `filter`.

Các triển khai Rx khác nhau đã tìm thấy một ngôi nhà rất hạnh phúc trong các hệ thống phân tán. Chúng cho phép chúng ta trừu tượng hóa các chi tiết về cách các cuộc gọi được thực hiện, và lý giải về mọi thứ dễ dàng hơn. Tôi quan sát kết quả của một cuộc gọi đến một dịch vụ hạ nguồn. Tôi không quan tâm nó là một cuộc gọi chặn hay không chặn, tôi chỉ đợi phản hồi và phản ứng. Vẻ đẹp là tôi có thể kết hợp nhiều cuộc gọi lại với nhau, làm cho việc xử lý các cuộc gọi đồng thời đến các dịch vụ hạ nguồn trở nên dễ dàng hơn nhiều.

Khi bạn thấy mình thực hiện nhiều cuộc gọi dịch vụ hơn, đặc biệt là khi thực hiện nhiều cuộc gọi để thực hiện một hoạt động duy nhất, hãy xem xét các mở rộng phản ứng cho `technology stack` bạn đã chọn. Bạn có thể ngạc nhiên về việc cuộc sống của bạn có thể trở nên đơn giản hơn bao nhiêu.

#### **DRY và những Nguy cơ của việc Tái sử dụng Mã nguồn trong Thế giới Microservice (DRY and the Perils of Code Reuse in a Microservice World)**

Một trong những từ viết tắt mà chúng ta, các nhà phát triển, nghe rất nhiều là **DRY**: đừng lặp lại chính mình (don't repeat yourself). Mặc dù định nghĩa của nó đôi khi được đơn giản hóa thành việc cố gắng tránh trùng lặp mã, DRY chính xác hơn có nghĩa là chúng ta muốn tránh trùng lặp **hành vi và kiến thức hệ thống** của mình. Việc có nhiều dòng mã làm cùng một việc làm cho `codebase` của bạn lớn hơn mức cần thiết, và do đó khó lý giải hơn. Khi bạn muốn thay đổi hành vi, và hành vi đó được nhân bản ở nhiều nơi trong hệ thống của bạn, rất dễ quên mọi nơi bạn cần thực hiện một thay đổi, điều này có thể dẫn đến lỗi. Vì vậy, sử dụng DRY như một câu thần chú, nói chung, là hợp lý.

DRY là thứ dẫn chúng ta đến việc tạo ra mã có thể được tái sử dụng. Chúng ta kéo mã trùng lặp vào các trừu tượng mà sau đó chúng ta có thể gọi từ nhiều nơi. Có lẽ chúng ta đi xa đến mức tạo ra một thư viện dùng chung mà chúng ta có thể sử dụng ở mọi nơi! Tuy nhiên, cách tiếp cận này có thể nguy hiểm một cách lừa dối trong một kiến trúc microservice.

Một trong những điều chúng ta muốn tránh bằng mọi giá là ghép nối quá mức một microservice và người tiêu dùng sao cho bất kỳ thay đổi nhỏ nào đối với chính microservice đó có thể gây ra những thay đổi không cần thiết cho người tiêu dùng. Tuy nhiên, đôi khi, việc sử dụng mã dùng chung có thể tạo ra chính sự ghép nối này. Ví dụ, tại một khách hàng, chúng tôi có một thư viện các đối tượng miền chung đại diện cho các thực thể cốt lõi được sử dụng trong hệ thống của chúng tôi. Thư viện này được sử dụng bởi tất cả các dịch vụ chúng tôi có. Nhưng khi một thay đổi được thực hiện cho một trong số chúng, tất cả các dịch vụ đều phải được cập nhật. Hệ thống của chúng tôi giao tiếp thông qua các hàng đợi thông điệp, cũng phải được làm trống nội dung không hợp lệ của chúng, và khốn cho bạn nếu bạn quên.

Nếu việc sử dụng mã dùng chung của bạn từng bị rò rỉ ra ngoài ranh giới dịch vụ của bạn, bạn đã giới thiệu một hình thức ghép nối tiềm năng. Sử dụng mã chung như các thư viện ghi nhật ký là ổn, vì chúng là các khái niệm nội bộ không thể nhìn thấy được từ thế giới bên ngoài. RealEstate.com.au sử dụng một mẫu dịch vụ được tùy chỉnh để giúp khởi tạo việc tạo dịch vụ mới. Thay vì chia sẻ mã này, công ty sao chép nó cho mỗi dịch vụ mới để đảm bảo rằng sự ghép nối không bị rò rỉ vào.

Quy tắc kinh nghiệm chung của tôi: đừng vi phạm DRY trong một microservice, nhưng hãy thoải mái về việc vi phạm DRY trên tất cả các dịch vụ. Những tệ nạn của việc ghép nối quá nhiều giữa các dịch vụ tồi tệ hơn nhiều so với những vấn đề gây ra bởi sự trùng lặp mã. Tuy nhiên, có một trường hợp sử dụng cụ thể đáng để khám phá thêm.

##### **Thư viện Máy khách (Client Libraries)**

Tôi đã nói chuyện với nhiều hơn một đội đã khăng khăng rằng việc tạo ra các thư viện máy khách cho các dịch vụ của bạn là một phần thiết yếu của việc tạo ra các dịch vụ ngay từ đầu. Lập luận là điều này làm cho việc sử dụng dịch vụ của bạn dễ dàng, và tránh sự trùng lặp mã cần thiết để tiêu thụ chính dịch vụ đó.

Tất nhiên, vấn đề là nếu cùng một người tạo ra cả API máy chủ và API máy khách, có nguy cơ là logic lẽ ra phải tồn tại trên máy chủ bắt đầu rò rỉ vào máy khách. Tôi nên biết: tôi đã tự mình làm điều này. Càng nhiều logic lọt vào thư viện máy khách, tính gắn kết càng bắt đầu bị phá vỡ, và bạn thấy mình phải thay đổi nhiều máy khách để triển khai các bản sửa lỗi cho máy chủ của mình. Bạn cũng hạn chế các lựa chọn công nghệ, đặc biệt nếu bạn bắt buộc rằng thư viện máy khách phải được sử dụng.

Một mô hình cho các thư viện máy khách mà tôi thích là mô hình cho Dịch vụ Web Amazon (AWS). Các cuộc gọi dịch vụ web `SOAP` hoặc `REST` cơ bản có thể được thực hiện trực tiếp, nhưng mọi người cuối cùng chỉ sử dụng một trong các bộ công cụ phát triển phần mềm (`SDKs`) khác nhau tồn tại, cung cấp các trừu tượng trên API cơ bản. Tuy nhiên, các `SDK` này được viết bởi cộng đồng hoặc những người của AWS không phải là những người làm việc trên chính API đó. Mức độ tách biệt này dường như hoạt động, và tránh được một số cạm bẫy của các thư viện máy khách. Một phần lý do điều này hoạt động tốt là máy khách chịu trách nhiệm khi việc nâng cấp xảy ra. Nếu bạn tự đi theo con đường của các thư viện máy khách, hãy chắc chắn rằng đây là trường hợp.

Netflix đặc biệt nhấn mạnh vào thư viện máy khách, nhưng tôi lo lắng rằng mọi người xem điều đó hoàn toàn qua lăng kính của việc tránh trùng lặp mã. Trên thực tế, các thư viện máy khách được Netflix sử dụng cũng nhiều (nếu không muốn nói là nhiều hơn) về việc đảm bảo độ tin cậy và khả năng mở rộng của hệ thống của họ. Các thư viện máy khách của Netflix xử lý việc khám phá dịch vụ, các chế độ lỗi, ghi nhật ký và các khía cạnh khác không thực sự liên quan đến bản chất của chính dịch vụ đó. Nếu không có những máy khách dùng chung này, sẽ khó đảm bảo rằng mỗi phần của giao tiếp máy khách/máy chủ hoạt động tốt ở quy mô lớn mà Netflix hoạt động. Việc sử dụng chúng tại Netflix chắc chắn đã giúp việc bắt đầu và chạy dễ dàng và tăng năng suất đồng thời đảm bảo hệ thống hoạt động tốt. Tuy nhiên, theo ít nhất một người tại Netflix, theo thời gian, điều này đã dẫn đến một mức độ ghép nối giữa máy khách và máy chủ đã gây ra vấn đề.

Nếu cách tiếp cận thư viện máy khách là điều bạn đang nghĩ đến, có thể quan trọng để tách mã máy khách để xử lý giao thức truyền tải cơ bản, có thể đối phó với những thứ như khám phá dịch vụ và lỗi, khỏi những thứ liên quan đến chính dịch vụ đích. Quyết định xem bạn có định khăng khăng sử dụng thư viện máy khách hay không, hoặc nếu bạn sẽ cho phép những người sử dụng các `technology stack` khác nhau thực hiện các cuộc gọi đến API cơ bản. Và cuối cùng, hãy chắc chắn rằng các máy khách chịu trách nhiệm khi nào nâng cấp thư viện máy khách của họ: chúng ta cần đảm bảo duy trì khả năng phát hành các dịch vụ của mình một cách độc lập với nhau!

#### **Truy cập theo Tham chiếu (Access by Reference)**

Một cân nhắc mà tôi muốn đề cập đến là cách chúng ta truyền thông tin về các thực thể miền của mình. Chúng ta cần chấp nhận ý tưởng rằng một microservice sẽ bao gồm vòng đời của các thực thể miền cốt lõi của chúng ta, như `Customer`. Chúng ta đã nói về tầm quan trọng của logic liên quan đến việc thay đổi `Customer` này được giữ trong dịch vụ khách hàng, và nếu chúng ta muốn thay đổi nó, chúng ta phải đưa ra một yêu cầu đến dịch vụ khách hàng. Nhưng cũng theo đó, chúng ta nên coi dịch vụ khách hàng là **nguồn chân lý (source of truth)** cho các `Customer`.

Khi chúng ta truy xuất một tài nguyên `Customer` nhất định từ dịch vụ khách hàng, chúng ta sẽ thấy tài nguyên đó trông như thế nào khi chúng ta thực hiện yêu cầu. Có thể sau khi chúng ta yêu cầu tài nguyên `Customer` đó, một cái gì đó khác đã thay đổi nó. Những gì chúng ta có thực chất là một **ký ức (memory)** về tài nguyên `Customer` đã từng trông như thế nào. Càng giữ ký ức này lâu, khả năng ký ức này sai càng cao. Tất nhiên, nếu chúng ta tránh yêu cầu dữ liệu nhiều hơn mức cần thiết, hệ thống của chúng ta có thể trở nên hiệu quả hơn nhiều.

Đôi khi ký ức này là đủ tốt. Những lúc khác, bạn cần biết liệu nó có thay đổi hay không. Vì vậy, dù bạn quyết định truyền đi một ký ức về một thực thể đã từng trông như thế nào, hãy chắc chắn rằng bạn cũng bao gồm một **tham chiếu (reference)** đến tài nguyên gốc để trạng thái mới có thể được truy xuất.

Hãy xem xét ví dụ nơi chúng ta yêu cầu dịch vụ email gửi một email khi một đơn hàng đã được vận chuyển. Bây giờ chúng ta có thể gửi trong yêu cầu đến dịch vụ email với địa chỉ email, tên và chi tiết đơn hàng của khách hàng. Tuy nhiên, nếu dịch vụ email thực sự đang xếp hàng các yêu cầu này, hoặc kéo chúng từ một hàng đợi, mọi thứ có thể thay đổi trong thời gian chờ đợi. Có thể hợp lý hơn nếu chỉ gửi một `URI` cho các tài nguyên `Customer` và `Order`, và để máy chủ email tự đi tìm chúng khi đến lúc gửi email.

Một lập luận phản bác tuyệt vời cho điều này xuất hiện khi chúng ta xem xét sự hợp tác dựa trên sự kiện. Với các sự kiện, chúng ta đang nói *điều này đã xảy ra*, nhưng chúng ta cần biết *cái gì* đã xảy ra. Ví dụ, nếu chúng ta đang nhận các cập nhật do một tài nguyên `Customer` thay đổi, có thể có giá trị đối với chúng ta khi biết `Customer` trông như thế nào khi sự kiện xảy ra. Miễn là chúng ta cũng nhận được một tham chiếu đến chính thực thể đó để chúng ta có thể tra cứu trạng thái hiện tại của nó, thì chúng ta có thể có được điều tốt nhất của cả hai thế giới.

Tất nhiên, có những sự đánh đổi khác cần được thực hiện ở đây, khi chúng ta truy cập theo tham chiếu. Nếu chúng ta luôn đến dịch vụ khách hàng để xem thông tin liên quan đến một `Customer` nhất định, tải trọng trên dịch vụ khách hàng có thể quá lớn. Nếu chúng ta cung cấp thông tin bổ sung khi tài nguyên được truy xuất, cho chúng ta biết tại thời điểm nào tài nguyên ở trạng thái đã cho và có lẽ chúng ta có thể coi thông tin này là **tươi (fresh)** trong bao lâu, thì chúng ta có thể làm rất nhiều với bộ nhớ đệm (`caching`) để giảm tải. `HTTP` cung cấp cho chúng ta rất nhiều hỗ trợ này ngay từ đầu với một loạt các điều khiển bộ nhớ đệm, một số trong đó chúng ta sẽ thảo luận chi tiết hơn trong **Chương 11**.

Một vấn đề khác là một số dịch vụ của chúng ta có thể không cần biết về toàn bộ tài nguyên `Customer`, và bằng cách khăng khăng rằng chúng phải đi tra cứu, chúng ta có khả năng tăng sự ghép nối. Ví dụ, có thể lập luận rằng dịch vụ email của chúng ta nên ngu ngốc hơn, và chúng ta chỉ nên gửi cho nó địa chỉ email và tên của khách hàng. Không có một quy tắc cứng nhắc nào ở đây, nhưng hãy rất cảnh giác khi truyền dữ liệu trong các yêu cầu khi bạn không biết độ tươi của nó.

#### **Phiên bản hóa (Versioning)**

Trong mỗi buổi nói chuyện mà tôi từng thực hiện về microservices, tôi đều được hỏi *làm thế nào để bạn thực hiện phiên bản hóa?* Mọi người có mối quan tâm chính đáng rằng cuối cùng họ sẽ phải thực hiện một thay đổi đối với giao diện của một dịch vụ, và họ muốn hiểu cách quản lý điều đó. Hãy cùng phân tích vấn đề một chút và xem xét các bước khác nhau chúng ta có thể thực hiện để xử lý nó.

##### **Trì hoãn nó càng lâu càng tốt (Defer It for as Long as Possible)**

Cách tốt nhất để giảm tác động của việc thực hiện các thay đổi gây đổ vỡ là tránh thực hiện chúng ngay từ đầu. Bạn có thể đạt được phần lớn điều này bằng cách chọn đúng công nghệ tích hợp, như chúng ta đã thảo luận trong suốt chương này. Tích hợp cơ sở dữ liệu là một ví dụ tuyệt vời về công nghệ có thể làm cho việc tránh các thay đổi gây đổ vỡ trở nên rất khó khăn. Mặt khác, `REST` lại giúp ích vì những thay đổi đối với chi tiết triển khai nội bộ ít có khả năng dẫn đến một thay đổi đối với giao diện dịch vụ.

Một chìa khóa khác để trì hoãn một thay đổi gây đổ vỡ là khuyến khích hành vi tốt ở các máy khách của bạn, và tránh để chúng ràng buộc quá chặt chẽ với các dịch vụ của bạn ngay từ đầu. Hãy xem xét dịch vụ email của chúng ta, công việc của nó là gửi email cho khách hàng của chúng ta theo thời gian. Nó được yêu cầu gửi một email xác nhận đơn hàng đã vận chuyển cho khách hàng có ID 1234. Nó đi và truy xuất khách hàng có ID đó, và nhận lại một cái gì đó giống như phản hồi được hiển thị trong Ví dụ 4-3.

**Ví dụ 4-3.** *Phản hồi mẫu từ dịch vụ khách hàng*
```xml
<customer>
    <firstname>Sam</firstname>
    <lastname>Newman</lastname>
    <email>sam@magpiebrain.com</email>
    <telephoneNumber>555-1234-5678</telephoneNumber>
</customer>
```
Bây giờ để gửi email, chúng ta chỉ cần các trường `firstname`, `lastname`, và `email`. Chúng ta không cần biết `telephoneNumber`. Chúng ta muốn chỉ đơn giản là lấy ra những trường chúng ta quan tâm, và bỏ qua phần còn lại. Một số công nghệ ràng buộc, đặc biệt là những công nghệ được sử dụng bởi các ngôn ngữ có kiểu mạnh, có thể cố gắng ràng buộc tất cả các trường dù người tiêu dùng có muốn chúng hay không. Điều gì xảy ra nếu chúng ta nhận ra rằng không ai đang sử dụng `telephoneNumber` và chúng ta quyết định loại bỏ nó? Điều này có thể khiến người tiêu dùng bị hỏng một cách không cần thiết.

Tương tự, điều gì sẽ xảy ra nếu chúng ta muốn tái cấu trúc đối tượng `Customer` của mình để hỗ trợ nhiều chi tiết hơn, có lẽ thêm một số cấu trúc bổ sung như trong Ví dụ 4-4? Dữ liệu mà dịch vụ email của chúng ta muốn vẫn ở đó, và vẫn có cùng tên, nhưng nếu mã của chúng ta đưa ra các giả định rất rõ ràng về nơi các trường `firstname` và `lastname` sẽ được lưu trữ, thì nó có thể bị hỏng một lần nữa. Trong trường hợp này, chúng ta thay vào đó có thể sử dụng `XPath` để lấy ra các trường chúng ta quan tâm, cho phép chúng ta không quan tâm đến vị trí của các trường, miễn là chúng ta có thể tìm thấy chúng. Mẫu hình này—của việc triển khai một trình đọc có khả năng bỏ qua các thay đổi chúng ta không quan tâm—là những gì Martin Fowler gọi là một **Trình đọc Khoan dung (Tolerant Reader)**.

**Ví dụ 4-4.** *Một tài nguyên Customer được tái cấu trúc: dữ liệu vẫn ở đó, nhưng người tiêu dùng của chúng ta có thể tìm thấy nó không?*
```xml
<customer>
    <naming>
        <firstname>Sam</firstname>
        <lastname>Newman</lastname>
        <nickname>Magpiebrain</nickname>
        <fullname>Sam "Magpiebrain" Newman</fullname>
    </naming>
    <email>sam@magpiebrain.com</email>
</customer>
```
Ví dụ về một máy khách cố gắng linh hoạt nhất có thể trong việc tiêu thụ một dịch vụ thể hiện **Luật của Postel (Postel’s Law)** (còn được gọi là nguyên tắc mạnh mẽ), trong đó nêu rõ: "Hãy bảo thủ trong những gì bạn làm, hãy tự do trong những gì bạn chấp nhận từ người khác." Bối cảnh ban đầu cho mảnh trí tuệ này là sự tương tác của các thiết bị qua mạng, nơi bạn nên mong đợi tất cả các loại điều kỳ lạ xảy ra. Trong bối cảnh của tương tác `request/response` của chúng ta, nó có thể dẫn chúng ta đến việc cố gắng hết sức để cho phép dịch vụ đang được tiêu thụ thay đổi mà không yêu cầu chúng ta phải thay đổi.

##### **Phát hiện sớm các Thay đổi Gây đổ vỡ (Catch Breaking Changes Early)**

Điều quan trọng là phải đảm bảo chúng ta phát hiện ra những thay đổi sẽ làm hỏng người tiêu dùng càng sớm càng tốt, bởi vì ngay cả khi chúng ta chọn công nghệ tốt nhất có thể, sự cố vẫn có thể xảy ra. Tôi rất ủng hộ việc sử dụng các **hợp đồng do người tiêu dùng điều khiển (consumer-driven contracts)**, mà chúng ta sẽ đề cập trong **Chương 7**, để giúp phát hiện những vấn đề này sớm. Nếu bạn đang hỗ trợ nhiều thư viện máy khách khác nhau, việc chạy các bài kiểm thử sử dụng mỗi thư viện bạn hỗ trợ chống lại dịch vụ mới nhất là một kỹ thuật khác có thể giúp ích. Một khi bạn nhận ra rằng bạn sẽ làm hỏng một người tiêu dùng, bạn có lựa chọn hoặc cố gắng tránh hoàn toàn sự cố hoặc chấp nhận nó và bắt đầu có những cuộc trò chuyện đúng đắn với những người chăm sóc các dịch vụ tiêu thụ.

##### **Sử dụng Phiên bản hóa Ngữ nghĩa (Use Semantic Versioning)**

Sẽ không tuyệt vời sao nếu với tư cách là một máy khách, bạn có thể chỉ cần nhìn vào số phiên bản của một dịch vụ và biết liệu bạn có thể tích hợp với nó không? **Phiên bản hóa ngữ nghĩa (Semantic versioning)** là một đặc tả cho phép chính xác điều đó. Với phiên bản hóa ngữ nghĩa, mỗi số phiên bản có dạng `MAJOR.MINOR.PATCH`.
*   Khi số `MAJOR` tăng lên, điều đó có nghĩa là các thay đổi không tương thích ngược đã được thực hiện.
*   Khi `MINOR` tăng lên, chức năng mới đã được thêm vào và nên tương thích ngược.
*   Cuối cùng, một thay đổi đối với `PATCH` cho biết rằng các bản sửa lỗi đã được thực hiện cho chức năng hiện có.

Để xem phiên bản hóa ngữ nghĩa hữu ích như thế nào, hãy xem xét một trường hợp sử dụng đơn giản. Ứng dụng trợ giúp của chúng ta được xây dựng để hoạt động với phiên bản 1.2.0 của dịch vụ khách hàng. Nếu một tính năng mới được thêm vào, khiến dịch vụ khách hàng thay đổi thành 1.3.0, ứng dụng trợ giúp của chúng ta sẽ không thấy thay đổi nào về hành vi và không nên được mong đợi phải thực hiện bất kỳ thay đổi nào. Tuy nhiên, chúng ta không thể đảm bảo rằng chúng ta có thể hoạt động với phiên bản 1.1.0 của dịch vụ khách hàng, vì chúng ta có thể dựa vào chức năng được thêm vào trong bản phát hành 1.2.0. Chúng ta cũng có thể mong đợi phải thực hiện các thay đổi đối với ứng dụng của mình nếu một bản phát hành 2.0.0 mới của dịch vụ khách hàng ra mắt.

Bạn có thể quyết định có một phiên bản ngữ nghĩa cho dịch vụ, hoặc thậm chí cho một điểm cuối riêng lẻ trên một dịch vụ nếu bạn đang đồng tồn tại chúng như được chi tiết trong phần tiếp theo.

Lược đồ phiên bản hóa này cho phép chúng ta gói rất nhiều thông tin và kỳ vọng chỉ trong ba trường. Đặc tả đầy đủ phác thảo một cách rất đơn giản các kỳ vọng mà các máy khách có thể có về những thay đổi đối với các con số này, và có thể đơn giản hóa quá trình giao tiếp về việc liệu các thay đổi có nên tác động đến người tiêu dùng hay không. Thật không may, tôi chưa thấy cách tiếp cận này được sử dụng đủ trong bối cảnh của các hệ thống phân tán.

##### **Cùng tồn tại các Điểm cuối Khác nhau (Coexist Different Endpoints)**

Nếu chúng ta đã làm tất cả những gì có thể để tránh đưa ra một thay đổi giao diện gây đổ vỡ, công việc tiếp theo của chúng ta là hạn chế tác động. Điều chúng ta muốn tránh là buộc người tiêu dùng phải nâng cấp đồng bộ (`lock-step`) với chúng ta, vì chúng ta luôn muốn duy trì khả năng phát hành các microservice một cách độc lập với nhau. Một cách tiếp cận mà tôi đã sử dụng thành công để xử lý điều này là cho cả giao diện cũ và mới cùng tồn tại trong cùng một dịch vụ đang chạy. Vì vậy, nếu chúng ta muốn phát hành một thay đổi gây đổ vỡ, chúng ta triển khai một phiên bản mới của dịch vụ phơi bày cả phiên bản cũ và mới của điểm cuối.

Điều này cho phép chúng ta đưa microservice mới ra ngoài càng sớm càng tốt, cùng với giao diện mới, nhưng cho người tiêu dùng thời gian để chuyển đổi. Một khi tất cả người tiêu dùng không còn sử dụng điểm cuối cũ nữa, bạn có thể loại bỏ nó cùng với bất kỳ mã liên quan nào, như được hiển thị trong Hình 4-5.

![alt text](<images/Screenshot from 2025-09-29 06-49-55.png>)

**Hình 4-5.** *Cùng tồn tại các phiên bản điểm cuối khác nhau cho phép người tiêu dùng di chuyển dần dần*

Lần cuối cùng tôi sử dụng cách tiếp cận này, chúng tôi đã tự đưa mình vào một chút rắc rối với số lượng người tiêu dùng chúng tôi có và số lượng các thay đổi gây đổ vỡ mà chúng tôi đã thực hiện. Điều này có nghĩa là chúng tôi thực sự đang cho cùng tồn tại ba phiên bản khác nhau của điểm cuối. Đây không phải là điều tôi khuyến nghị! Việc giữ lại tất cả mã nguồn và các bài kiểm thử liên quan để đảm bảo tất cả chúng đều hoạt động là một gánh nặng bổ sung. Để làm cho điều này dễ quản lý hơn, chúng tôi đã chuyển đổi nội bộ tất cả các yêu cầu đến điểm cuối V1 thành yêu cầu V2, và sau đó các yêu cầu V2 thành điểm cuối V3. Điều này có nghĩa là chúng tôi có thể phân định rõ ràng mã nào sẽ được gỡ bỏ khi (các) điểm cuối cũ chết đi.

Đây thực chất là một ví dụ của mẫu hình **mở rộng và thu hẹp (expand and contract)**, cho phép chúng ta đưa vào các thay đổi gây đổ vỡ theo từng giai đoạn. Chúng ta *mở rộng* các khả năng chúng ta cung cấp, hỗ trợ cả cách làm cũ và mới. Một khi những người tiêu dùng cũ làm mọi thứ theo cách mới, chúng ta *thu hẹp* API của mình, loại bỏ chức năng cũ.

Nếu bạn định cho các điểm cuối cùng tồn tại, bạn cần một cách để người gọi định tuyến các yêu cầu của họ một cách tương ứng. Đối với các hệ thống sử dụng `HTTP`, tôi đã thấy điều này được thực hiện với cả số phiên bản trong tiêu đề yêu cầu và cả trong chính `URI`—ví dụ, `/v1/customer/` hoặc `/v2/customer/`. Tôi phân vân về cách tiếp cận nào là hợp lý nhất. Một mặt, tôi thích các `URI` là mờ đục để ngăn cản các máy khách mã hóa cứng các mẫu `URI`, nhưng mặt khác, cách tiếp cận này làm cho mọi thứ rất rõ ràng và có thể đơn giản hóa việc định tuyến yêu cầu.

Đối với RPC, mọi thứ có thể phức tạp hơn một chút. Tôi đã xử lý điều này với `protocol buffers` bằng cách đặt các phương thức của mình trong các không gian tên khác nhau—ví dụ, `v1.createCustomer` và `v2.createCustomer`—nhưng khi bạn đang cố gắng hỗ trợ các phiên bản khác nhau của cùng một loại đang được gửi qua mạng, điều này có thể trở nên rất đau đớn.

##### **Sử dụng Nhiều Phiên bản Dịch vụ Đồng thời (Use Multiple Concurrent Service Versions)**

Một giải pháp phiên bản hóa khác thường được trích dẫn là có các phiên bản khác nhau của dịch vụ hoạt động cùng một lúc, và để những người tiêu dùng cũ hơn định tuyến lưu lượng truy cập của họ đến phiên bản cũ hơn, với những phiên bản mới hơn thấy phiên bản mới, như được hiển thị trong Hình 4-6. Đây là cách tiếp cận được Netflix sử dụng một cách tiết kiệm trong các tình huống mà chi phí thay đổi những người tiêu dùng cũ là quá cao, đặc biệt là trong các trường hợp hiếm hoi khi các thiết bị kế thừa vẫn bị ràng buộc vào các phiên bản API cũ hơn. Cá nhân tôi, tôi không phải là một người hâm mộ ý tưởng này, và hiểu tại sao Netflix hiếm khi sử dụng nó.
*   **Thứ nhất**, nếu tôi cần sửa một lỗi nội bộ trong dịch vụ của mình, bây giờ tôi phải sửa và triển khai hai bộ dịch vụ khác nhau. Điều này có thể có nghĩa là tôi phải phân nhánh `codebase` cho dịch vụ của mình, và điều này luôn có vấn đề.
*   **Thứ hai**, nó có nghĩa là tôi cần sự thông minh để xử lý việc hướng người tiêu dùng đến đúng microservice. Hành vi này chắc chắn sẽ nằm trong `middleware` ở đâu đó hoặc một loạt các tập lệnh nginx, làm cho việc lý giải về hành vi của hệ thống trở nên khó khăn hơn.
*   **Cuối cùng**, hãy xem xét bất kỳ trạng thái bền vững nào mà dịch vụ của chúng ta có thể quản lý. Các khách hàng được tạo bởi một trong hai phiên bản của dịch vụ cần được lưu trữ và hiển thị cho tất cả các dịch vụ, bất kể phiên bản nào được sử dụng để tạo dữ liệu ngay từ đầu. Điều này có thể là một nguồn phức tạp bổ sung.

![alt text](<images/Screenshot from 2025-09-29 06-50-03.png>)

**Hình 4-6.** *Chạy nhiều phiên bản của cùng một dịch vụ để hỗ trợ các điểm cuối cũ*

Việc cho các phiên bản dịch vụ đồng thời cùng tồn tại trong một khoảng thời gian ngắn có thể hoàn toàn hợp lý, đặc biệt là khi bạn đang làm những việc như triển khai **blue/green** hoặc **canary releases** (chúng ta sẽ thảo luận về những mẫu hình này nhiều hơn trong **Chương 7**). Trong những tình huống này, chúng ta có thể cho các phiên bản cùng tồn tại chỉ trong vài phút hoặc có lẽ là vài giờ, và thông thường sẽ chỉ có hai phiên bản khác nhau của dịch vụ hiện diện cùng một lúc. Càng mất nhiều thời gian để bạn nâng cấp người tiêu dùng lên phiên bản mới hơn và phát hành, bạn càng nên xem xét việc cho các điểm cuối khác nhau cùng tồn tại trong cùng một microservice thay vì cho các phiên bản hoàn toàn khác nhau cùng tồn tại. Tôi vẫn chưa bị thuyết phục rằng công việc này đáng giá cho một dự án trung bình.

---
#### **Giao diện Người dùng (User Interfaces)**

Cho đến nay, chúng ta chưa thực sự đề cập đến thế giới của giao diện người dùng. Một vài người trong số các bạn có thể chỉ đang cung cấp một API lạnh lùng, cứng nhắc, lâm sàng cho khách hàng của mình, nhưng nhiều người trong chúng ta thấy mình muốn tạo ra các giao diện người dùng đẹp, chức năng sẽ làm hài lòng khách hàng của mình. Nhưng chúng ta thực sự cần phải suy nghĩ về chúng trong bối cảnh tích hợp. Rốt cuộc, giao diện người dùng là nơi chúng ta sẽ kéo tất cả các microservice này lại với nhau thành một cái gì đó có ý nghĩa đối với khách hàng của chúng ta.

Trong quá khứ, khi tôi mới bắt đầu làm việc với máy tính, chúng ta chủ yếu nói về các máy khách béo, lớn chạy trên máy tính để bàn của chúng ta. Tôi đã dành nhiều giờ với Motif và sau đó là Swing để cố gắng làm cho phần mềm của mình dễ sử dụng nhất có thể. Thường thì các hệ thống này chỉ dùng để tạo và thao tác các tệp cục bộ, nhưng nhiều trong số chúng có một thành phần phía máy chủ. Công việc đầu tiên của tôi tại ThoughtWorks liên quan đến việc tạo ra một hệ thống điểm bán hàng điện tử dựa trên Swing chỉ là một phần của một số lượng lớn các bộ phận chuyển động, hầu hết trong số đó đều ở trên máy chủ.

Sau đó là Web. Chúng ta bắt đầu nghĩ về các giao diện người dùng của mình như là **mỏng (thin)** thay vào đó, với nhiều logic hơn ở phía máy chủ. Ban đầu, các chương trình phía máy chủ của chúng ta kết xuất toàn bộ trang và gửi nó đến trình duyệt máy khách, mà không làm gì nhiều. Bất kỳ tương tác nào cũng được xử lý ở phía máy chủ, thông qua các `GET` và `POST` được kích hoạt bởi người dùng nhấp vào các liên kết hoặc điền vào các biểu mẫu. Theo thời gian, JavaScript trở thành một lựa chọn phổ biến hơn để thêm hành vi động vào giao diện người dùng dựa trên trình duyệt, và một số ứng dụng bây giờ có thể được cho là béo như các máy khách máy tính để bàn cũ.

##### **Hướng tới Kỹ thuật số (Toward Digital)**

Trong vài năm qua, các tổ chức đã bắt đầu chuyển từ việc suy nghĩ rằng web hoặc di động nên được đối xử khác nhau; thay vào đó, họ đang suy nghĩ về **kỹ thuật số (digital)** một cách toàn diện hơn. Cách tốt nhất để khách hàng của chúng ta sử dụng các dịch vụ chúng ta cung cấp là gì? Và điều đó ảnh hưởng gì đến kiến trúc hệ thống của chúng ta? Sự hiểu biết rằng chúng ta không thể dự đoán chính xác cách một khách hàng có thể tương tác với công ty của mình đã thúc đẩy việc áp dụng các API chi tiết hơn, như những API được cung cấp bởi microservices. Bằng cách kết hợp các khả năng mà các dịch vụ của chúng ta phơi bày theo những cách khác nhau, chúng ta có thể tạo ra các trải nghiệm khác nhau cho khách hàng của mình trên ứng dụng máy tính để bàn, thiết bị di động, thiết bị đeo được, hoặc thậm chí ở dạng vật lý nếu họ ghé thăm cửa hàng truyền thống của chúng ta.

Vì vậy, hãy nghĩ về các giao diện người dùng như các **lớp σύνθεσης (compositional layers)**—những nơi chúng ta kết hợp các sợi khác nhau của các khả năng chúng ta cung cấp. Với ý nghĩ đó, làm thế nào để chúng ta kéo tất cả các sợi này lại với nhau?

##### **Ràng buộc (Constraints)**

Ràng buộc là các hình thức khác nhau mà người dùng của chúng ta tương tác với hệ thống của chúng ta. Trên một ứng dụng web máy tính để bàn, chẳng hạn, chúng ta xem xét các ràng buộc như trình duyệt mà khách truy cập đang sử dụng, hoặc độ phân giải của họ. Nhưng di động đã mang đến một loạt các ràng buộc mới. Cách các ứng dụng di động của chúng ta giao tiếp với máy chủ có thể có tác động. Nó không chỉ là về những lo ngại về băng thông thuần túy, nơi những hạn chế của mạng di động có thể đóng một vai trò. Các loại tương tác khác nhau có thể làm cạn kiệt pin, dẫn đến một số khách hàng bực bội.

Bản chất của các tương tác cũng thay đổi. Tôi không thể dễ dàng nhấp chuột phải trên một máy tính bảng. Trên một chiếc điện thoại di động, tôi có thể muốn thiết kế giao diện của mình để được sử dụng chủ yếu bằng một tay, với hầu hết các hoạt động được điều khiển bằng một ngón tay cái. Ở nơi khác, tôi có thể cho phép mọi người tương tác với các dịch vụ qua SMS ở những nơi băng thông là một điều xa xỉ—việc sử dụng SMS như một giao diện là rất lớn ở Nam bán cầu, chẳng hạn.

Vì vậy, mặc dù các dịch vụ cốt lõi của chúng ta—sản phẩm cốt lõi của chúng ta—có thể giống nhau, chúng ta cần một cách để điều chỉnh chúng cho các ràng buộc khác nhau tồn tại cho mỗi loại giao diện. Khi chúng ta xem xét các phong cách khác nhau của việc kết hợp giao diện người dùng, chúng ta cần đảm bảo rằng chúng giải quyết được thách thức này. Hãy xem xét một vài mô hình giao diện người dùng để xem điều này có thể đạt được như thế nào.

##### **Kết hợp API (API Composition)**

Giả sử rằng các dịch vụ của chúng ta đã nói chuyện `XML` hoặc `JSON` với nhau qua `HTTP`, một tùy chọn rõ ràng có sẵn cho chúng ta là để giao diện người dùng của chúng ta tương tác trực tiếp với các API này, như trong Hình 4-7. Một giao diện người dùng dựa trên web có thể sử dụng các yêu cầu `GET` JavaScript để truy xuất dữ liệu, hoặc các yêu cầu `POST` để thay đổi nó. Ngay cả đối với các ứng dụng di động gốc, việc khởi tạo các giao tiếp `HTTP` cũng khá đơn giản. Giao diện người dùng sau đó sẽ cần tạo ra các thành phần khác nhau tạo nên giao diện, xử lý việc đồng bộ hóa trạng thái và những thứ tương tự với máy chủ. Nếu chúng ta đang sử dụng một giao thức nhị phân để giao tiếp từ dịch vụ đến dịch vụ, điều này sẽ khó khăn hơn cho các máy khách dựa trên web, nhưng có thể ổn đối với các thiết bị di động gốc.

Có một vài nhược điểm với cách tiếp cận này.
*   **Thứ nhất**, chúng ta có ít khả năng để điều chỉnh các phản hồi cho các loại thiết bị khác nhau. Ví dụ, khi tôi truy xuất một bản ghi khách hàng, tôi có cần lấy lại tất cả cùng một dữ liệu cho một cửa hàng di động như tôi làm cho một ứng dụng trợ giúp không? Một giải pháp cho cách tiếp cận này là cho phép người tiêu dùng chỉ định những trường nào cần lấy lại khi họ thực hiện một yêu cầu, nhưng điều này giả định rằng mỗi dịch vụ đều hỗ trợ hình thức tương tác này.
*   **Một câu hỏi quan trọng khác**: ai tạo ra giao diện người dùng? Những người chăm sóc các dịch vụ bị tách rời khỏi cách các dịch vụ của họ được hiển thị cho người dùng—ví dụ, nếu một đội khác đang tạo giao diện người dùng, chúng ta có thể đang trôi dạt trở lại những ngày xưa tồi tệ của kiến trúc phân tầng, nơi việc thực hiện ngay cả những thay đổi nhỏ cũng đòi hỏi các yêu cầu thay đổi cho nhiều đội.

![alt text](<images/Screenshot from 2025-09-29 06-50-07.png>)

**Hình 4-7.** *Sử dụng nhiều API để trình bày một giao diện người dùng*

Giao tiếp này cũng có thể khá "lắm lời" (`chatty`). Mở nhiều cuộc gọi trực tiếp đến các dịch vụ có thể khá tốn kém cho các thiết bị di động, và có thể là một cách sử dụng rất không hiệu quả gói dữ liệu di động của khách hàng! Việc có một **cổng API (API gateway)** có thể giúp ích ở đây, vì bạn có thể phơi bày các cuộc gọi tổng hợp nhiều cuộc gọi cơ bản, mặc dù bản thân điều đó cũng có thể có một số nhược điểm mà chúng ta sẽ khám phá ngay sau đây.

##### **Kết hợp Mảnh giao diện người dùng (UI Fragment Composition)**

Thay vì để giao diện người dùng của chúng ta thực hiện các cuộc gọi API và ánh xạ mọi thứ trở lại các điều khiển giao diện người dùng, chúng ta có thể để các dịch vụ của mình cung cấp các phần của giao diện người dùng trực tiếp, và sau đó chỉ cần kéo các mảnh này vào để tạo ra một giao diện người dùng, như trong Hình 4-8. Hãy tưởng tượng, ví dụ, rằng dịch vụ đề xuất cung cấp một `widget` đề xuất được kết hợp với các điều khiển hoặc mảnh giao diện người dùng khác để tạo ra một giao diện người dùng tổng thể. Nó có thể được kết xuất như một hộp trên một trang web cùng với các nội dung khác.

Một biến thể của cách tiếp cận này có thể hoạt động tốt là lắp ráp một loạt các phần thô hơn của một giao diện người dùng. Vì vậy, thay vì tạo ra các `widget` nhỏ, bạn đang lắp ráp toàn bộ các ô của một ứng dụng máy khách dày, hoặc có lẽ là một tập hợp các trang cho một trang web.

Các mảnh thô hơn này được phục vụ từ các ứng dụng phía máy chủ, mà lần lượt đang thực hiện các cuộc gọi API phù hợp. Mô hình này hoạt động tốt nhất khi các mảnh phù hợp tốt với quyền sở hữu của đội. Ví dụ, có lẽ đội ngũ chăm sóc quản lý đơn hàng trong cửa hàng âm nhạc phục vụ tất cả các trang liên quan đến quản lý đơn hàng.

![alt text](<images/Screenshot from 2025-09-29 06-50-15.png>)

**Hình 4-8.** *Các dịch vụ trực tiếp phục vụ các thành phần giao diện người dùng để lắp ráp*

Bạn vẫn cần một loại lớp lắp ráp nào đó để kéo các phần này lại với nhau. Điều này có thể đơn giản như một số tạo mẫu phía máy chủ, hoặc, nơi mỗi bộ trang đến từ một ứng dụng khác nhau, có lẽ bạn sẽ cần một số định tuyến `URI` thông minh.

Một trong những lợi thế chính của cách tiếp cận này là cùng một đội thực hiện các thay đổi đối với các dịch vụ cũng có thể chịu trách nhiệm thực hiện các thay đổi đối với các phần đó của giao diện người dùng. Nó cho phép chúng ta đưa ra các thay đổi nhanh hơn. Nhưng có một số vấn đề với cách tiếp cận này.
*   **Thứ nhất**, việc đảm bảo tính nhất quán của trải nghiệm người dùng là điều chúng ta cần giải quyết. Người dùng muốn có một trải nghiệm liền mạch, không cảm thấy rằng các phần khác nhau của giao diện hoạt động theo những cách khác nhau, hoặc trình bày một ngôn ngữ thiết kế khác nhau. Có những kỹ thuật để tránh vấn đề này, chẳng hạn như các hướng dẫn phong cách sống (`living style guides`), nơi các tài sản như các thành phần `HTML`, `CSS`, và hình ảnh có thể được chia sẻ để giúp mang lại một mức độ nhất quán nào đó.
*   **Một vấn đề khác** khó giải quyết hơn. Điều gì xảy ra với các ứng dụng gốc hoặc các máy khách dày? Chúng ta không thể phục vụ các thành phần giao diện người dùng. Chúng ta có thể sử dụng một cách tiếp cận lai và sử dụng các ứng dụng gốc để phục vụ các thành phần `HTML`, nhưng cách tiếp cận này đã được chứng minh nhiều lần là có nhược điểm. Vì vậy, nếu bạn cần một trải nghiệm gốc, chúng ta sẽ phải quay trở lại một cách tiếp cận nơi ứng dụng giao diện người dùng thực hiện các cuộc gọi API và tự xử lý giao diện người dùng. Nhưng ngay cả khi chúng ta chỉ xem xét các giao diện người dùng chỉ dành cho web, chúng ta vẫn có thể muốn các xử lý rất khác nhau cho các loại thiết bị khác nhau. Tất nhiên, việc xây dựng các thành phần đáp ứng có thể giúp ích.

Có một vấn đề chính với cách tiếp cận này mà tôi không chắc có thể giải quyết được. Đôi khi các khả năng được cung cấp bởi một dịch vụ không phù hợp gọn gàng vào một `widget` hoặc một trang. Chắc chắn, tôi có thể muốn hiển thị các đề xuất trong một hộp trên một trang trên trang web của chúng ta, nhưng điều gì sẽ xảy ra nếu tôi muốn lồng ghép các đề xuất động ở nơi khác? Khi tôi tìm kiếm, tôi muốn tính năng gõ trước tự động kích hoạt các đề xuất mới, chẳng hạn. Càng có nhiều hình thức tương tác cắt ngang, mô hình này càng ít có khả năng phù hợp và càng có nhiều khả năng chúng ta sẽ quay trở lại chỉ thực hiện các cuộc gọi API.

##### **Backend cho Frontend (Backends for Frontends)**

Một giải pháp phổ biến cho vấn đề giao diện "lắm lời" với các dịch vụ backend, hoặc nhu cầu thay đổi nội dung cho các loại thiết bị khác nhau, là có một điểm cuối tổng hợp phía máy chủ, hoặc **cổng API (API gateway)**. Điều này có thể sắp xếp nhiều cuộc gọi backend, thay đổi và tổng hợp nội dung nếu cần cho các thiết bị khác nhau, và phục vụ nó, như chúng ta thấy trong Hình 4-9. Tôi đã thấy cách tiếp cận này dẫn đến thảm họa khi các điểm cuối phía máy chủ này trở thành các lớp dày với quá nhiều hành vi. Chúng cuối cùng được quản lý bởi các đội riêng biệt, và trở thành một nơi khác mà logic phải thay đổi bất cứ khi nào một số chức năng thay đổi.

![alt text](<images/Screenshot from 2025-09-29 06-50-23.png>)

**Hình 4-9.** *Sử dụng một cổng thông tin nguyên khối duy nhất để xử lý các cuộc gọi đến/từ các giao diện người dùng*

Vấn đề có thể xảy ra là thông thường chúng ta sẽ có một lớp khổng lồ cho tất cả các dịch vụ của mình. Điều này dẫn đến mọi thứ bị ném vào cùng nhau, và đột nhiên chúng ta bắt đầu mất đi sự cô lập của các giao diện người dùng khác nhau của mình, hạn chế khả năng phát hành chúng một cách độc lập. Một mô hình mà tôi ưa thích và đã thấy hoạt động tốt là hạn chế việc sử dụng các backend này cho một giao diện người dùng hoặc ứng dụng cụ thể, như chúng ta thấy trong Hình 4-10.

![alt text](<images/Screenshot from 2025-09-29 06-50-30.png>)

**Hình 4-10.** *Sử dụng các backend chuyên dụng cho các frontend*

Mẫu hình này đôi khi được gọi là **backends for frontends (BFFs)**. Nó cho phép đội ngũ tập trung vào bất kỳ giao diện người dùng nào cũng xử lý các thành phần phía máy chủ của riêng mình. Bạn có thể xem các backend này như là các phần của giao diện người dùng tình cờ được nhúng vào máy chủ. Một số loại giao diện người dùng có thể cần một dấu chân phía máy chủ tối thiểu, trong khi những loại khác có thể cần nhiều hơn rất nhiều. Nếu bạn cần một lớp xác thực và ủy quyền API, lớp này có thể nằm giữa các BFF và giao diện người dùng của chúng ta. Chúng ta sẽ khám phá điều này nhiều hơn trong **Chương 9**.

Nguy cơ với cách tiếp cận này cũng giống như với bất kỳ lớp tổng hợp nào; nó có thể đảm nhận logic mà nó không nên. Logic nghiệp vụ cho các khả năng khác nhau mà các backend này sử dụng nên nằm trong chính các dịch vụ đó. Các BFF này chỉ nên chứa hành vi cụ thể để cung cấp một trải nghiệm người dùng cụ thể.

##### **Một Cách tiếp cận Lai (A Hybrid Approach)**

Nhiều tùy chọn nói trên không cần phải là "một kích cỡ cho tất cả". Tôi có thể thấy một tổ chức áp dụng cách tiếp cận lắp ráp dựa trên mảnh để tạo một trang web, nhưng sử dụng cách tiếp cận backends-for-frontends khi nói đến ứng dụng di động của họ. Điểm mấu chốt là chúng ta cần giữ lại tính gắn kết của các khả năng cơ bản mà chúng ta cung cấp cho người dùng của mình. Chúng ta cần đảm bảo rằng logic liên quan đến việc đặt hàng âm nhạc hoặc thay đổi chi tiết khách hàng nằm bên trong các dịch vụ xử lý các hoạt động đó, và không bị bôi bẩn khắp hệ thống của chúng ta. Việc tránh bẫy đặt quá nhiều hành vi vào bất kỳ lớp trung gian nào là một hành động cân bằng tinh tế.

#### **Tích hợp với Phần mềm của Bên thứ ba (Integrating with Third-Party Software)**

Chúng ta đã xem xét các phương pháp để chia nhỏ các hệ thống hiện có nằm dưới sự kiểm soát của chúng ta, nhưng còn khi chúng ta không thể thay đổi những thứ chúng ta nói chuyện thì sao? Vì nhiều lý do hợp lệ, các tổ chức mà chúng ta làm việc mua phần mềm thương mại có sẵn (`commercial off-the-shelf software - COTS`) hoặc sử dụng các dịch vụ phần mềm như một dịch vụ (`software as a service - SaaS`) mà chúng ta có ít quyền kiểm soát. Vậy làm thế nào để chúng ta tích hợp với chúng một cách hợp lý?

Nếu bạn đang đọc cuốn sách này, có lẽ bạn làm việc tại một tổ chức viết mã. Bạn có thể viết phần mềm cho các mục đích nội bộ của riêng mình hoặc cho một khách hàng bên ngoài, hoặc cả hai. Tuy nhiên, ngay cả khi bạn là một tổ chức có khả năng tạo ra một lượng đáng kể phần mềm tùy chỉnh, bạn vẫn sẽ sử dụng các sản phẩm phần mềm được cung cấp bởi các bên bên ngoài, cho dù chúng là thương mại hay mã nguồn mở. Tại sao lại như vậy?

*   **Thứ nhất**, tổ chức của bạn gần như chắc chắn có nhu cầu về phần mềm lớn hơn khả năng đáp ứng nội bộ. Hãy nghĩ về tất cả các sản phẩm bạn sử dụng, từ các công cụ năng suất văn phòng như Excel đến các hệ điều hành đến các hệ thống tính lương. Việc tạo ra tất cả những thứ đó để sử dụng riêng của bạn sẽ là một công việc khổng lồ.
*   **Thứ hai**, và quan trọng nhất, nó sẽ không hiệu quả về mặt chi phí! Chi phí để bạn tự xây dựng hệ thống email của riêng mình, ví dụ, có khả năng vượt xa chi phí sử dụng một sự kết hợp hiện có của máy chủ mail và máy khách, ngay cả khi bạn chọn các tùy chọn thương mại.

Khách hàng của tôi thường vật lộn với câu hỏi "Nên xây dựng, hay nên mua?" Nói chung, lời khuyên mà tôi và các đồng nghiệp của tôi đưa ra khi có cuộc trò chuyện này với một tổ chức doanh nghiệp trung bình là "Hãy xây dựng nếu nó là duy nhất đối với những gì bạn làm, và có thể được coi là một tài sản chiến lược; hãy mua nếu việc sử dụng công cụ của bạn không quá đặc biệt."

Ví dụ, một tổ chức trung bình sẽ không coi hệ thống tính lương của mình là một tài sản chiến lược. Mọi người trên toàn thế giới nhìn chung đều được trả lương theo cùng một cách. Tương tự, hầu hết các tổ chức có xu hướng mua các hệ thống quản lý nội dung (`content management systems - CMSes`) có sẵn, vì việc sử dụng một công cụ như vậy không được coi là một cái gì đó quan trọng đối với doanh nghiệp của họ. Mặt khác, tôi đã tham gia vào việc xây dựng lại trang web của *The Guardian* từ sớm, và ở đó quyết định đã được đưa ra để xây dựng một hệ thống quản lý nội dung riêng, vì nó là cốt lõi của hoạt động kinh doanh của tờ báo.

Vì vậy, quan niệm rằng chúng ta thỉnh thoảng sẽ gặp phần mềm thương mại, của bên thứ ba là hợp lý, và đáng được hoan nghênh. Tuy nhiên, nhiều người trong chúng ta cuối cùng lại nguyền rủa một số hệ thống này. Tại sao lại như vậy?

##### **Thiếu Kiểm soát (Lack of Control)**

Một thách thức liên quan đến việc tích hợp và mở rộng các khả năng của các sản phẩm COTS như CMS hoặc các công cụ SaaS là thông thường nhiều quyết định kỹ thuật đã được đưa ra cho bạn. Làm thế nào để bạn tích hợp với công cụ? Đó là quyết định của nhà cung cấp. Bạn có thể sử dụng ngôn ngữ lập trình nào để mở rộng công cụ? Tùy thuộc vào nhà cung cấp. Bạn có thể lưu trữ cấu hình cho công cụ trong kiểm soát phiên bản, và xây dựng lại từ đầu, để cho phép tích hợp liên tục các tùy chỉnh không? Nó phụ thuộc vào các lựa chọn mà nhà cung cấp đưa ra.

Nếu bạn may mắn, việc làm việc với công cụ từ quan điểm phát triển dễ dàng—hay khó khăn—như thế nào đã được xem xét như một phần của quá trình lựa chọn công cụ. Nhưng ngay cả khi đó, bạn thực chất đang nhượng lại một mức độ kiểm soát nào đó cho một bên bên ngoài. Bí quyết là đưa công việc tích hợp và tùy chỉnh trở lại theo các điều kiện của riêng bạn.

##### **Tùy chỉnh (Customization)**

Nhiều công cụ mà các tổ chức doanh nghiệp mua tự bán mình dựa trên khả năng được tùy chỉnh rất nhiều *chỉ dành cho bạn*. Hãy cẩn thận! Thường thì, do bản chất của chuỗi công cụ bạn có quyền truy cập, chi phí tùy chỉnh có thể đắt hơn việc xây dựng một cái gì đó riêng từ đầu! Nếu bạn đã quyết định mua một sản phẩm nhưng các khả năng cụ thể mà nó cung cấp không quá đặc biệt đối với bạn, có thể hợp lý hơn nếu thay đổi cách tổ chức của bạn hoạt động thay vì bắt tay vào việc tùy chỉnh phức tạp.

Các hệ thống quản lý nội dung là một ví dụ tuyệt vời về mối nguy hiểm này. Tôi đã làm việc với nhiều CMS mà theo thiết kế không hỗ trợ tích hợp liên tục, có các API tồi tệ, và ngay cả một bản nâng cấp nhỏ trong công cụ cơ bản cũng có thể làm hỏng bất kỳ tùy chỉnh nào bạn đã thực hiện.

Salesforce đặc biệt phiền phức về mặt này. Trong nhiều năm, nó đã thúc đẩy nền tảng Force.com của mình, yêu cầu sử dụng một ngôn ngữ lập trình, Apex, chỉ tồn tại trong hệ sinh thái Force.com!

##### **Mì Spaghetti Tích hợp (Integration Spaghetti)**

Một thách thức khác là cách bạn tích hợp với công cụ. Như chúng ta đã thảo luận trước đó, việc suy nghĩ cẩn thận về cách bạn tích hợp giữa các dịch vụ là quan trọng, và lý tưởng nhất là bạn muốn chuẩn hóa trên một số lượng nhỏ các loại tích hợp. Nhưng nếu một sản phẩm quyết định sử dụng một giao thức nhị phân độc quyền, một sản phẩm khác một hương vị nào đó của `SOAP`, và một sản phẩm khác `XML-RPC`, bạn còn lại gì? Thậm chí tệ hơn là các công cụ cho phép bạn truy cập ngay vào các kho dữ liệu cơ bản của chúng, dẫn đến tất cả các vấn đề ghép nối tương tự mà chúng ta đã thảo luận trước đó.

#### **Theo Điều kiện của Riêng bạn (On Your Own Terms)**

Các sản phẩm COTS và SAAS hoàn toàn có vị trí của chúng, và việc xây dựng mọi thứ từ đầu không khả thi (hoặc hợp lý) đối với hầu hết chúng ta. Vậy làm thế nào để chúng ta giải quyết những thách thức này? Chìa khóa là chuyển mọi thứ trở lại theo các điều kiện của riêng bạn.

Ý tưởng cốt lõi ở đây là thực hiện bất kỳ tùy chỉnh nào trên một nền tảng bạn kiểm soát, và hạn chế số lượng người tiêu dùng khác nhau của chính công cụ đó. Để khám phá ý tưởng này một cách chi tiết, hãy xem xét một vài ví dụ.

##### **Ví dụ: CMS như một dịch vụ (Example: CMS as a service)**

Theo kinh nghiệm của tôi, CMS là một trong những sản phẩm được sử dụng phổ biến nhất cần được tùy chỉnh hoặc tích hợp. Lý do cho điều này là trừ khi bạn muốn một trang web tĩnh cơ bản, một tổ chức doanh nghiệp trung bình muốn làm phong phú thêm chức năng của trang web của mình bằng nội dung động như hồ sơ khách hàng hoặc các sản phẩm mới nhất. Nguồn của nội dung động này thường là các dịch vụ khác bên trong tổ chức, mà bạn có thể đã tự xây dựng.

Sự cám dỗ—và thường là điểm bán hàng của CMS—là bạn có thể tùy chỉnh CMS để lấy tất cả nội dung đặc biệt này và hiển thị nó cho thế giới bên ngoài. Tuy nhiên, môi trường phát triển cho một CMS trung bình là *tồi tệ*.

Hãy xem xét những gì một CMS trung bình chuyên về, và những gì chúng ta có lẽ đã mua nó vì: tạo nội dung và quản lý nội dung. Hầu hết các CMS thậm chí còn khá tệ trong việc bố trí trang, thường cung cấp các công cụ kéo và thả không đạt yêu cầu. Và ngay cả khi đó, bạn cuối cùng cũng cần phải có một người hiểu `HTML` và `CSS` để tinh chỉnh các mẫu CMS. Chúng có xu hướng là những nền tảng tồi tệ để xây dựng mã tùy chỉnh.

Câu trả lời? Đặt một dịch vụ của riêng bạn ở phía trước CMS để cung cấp trang web cho thế giới bên ngoài, như được hiển thị trong Hình 4-11. Hãy coi CMS như một dịch vụ có vai trò là cho phép tạo và truy xuất nội dung. Trong dịch vụ của riêng bạn, bạn viết mã và tích hợp với các dịch vụ theo cách bạn muốn. Bạn có quyền kiểm soát việc mở rộng quy mô trang web (nhiều CMS thương mại cung cấp các tiện ích bổ sung độc quyền của riêng họ để xử lý tải), và bạn có thể chọn hệ thống tạo mẫu có ý nghĩa.

Hầu hết các CMS cũng cung cấp các API để cho phép tạo nội dung, vì vậy bạn cũng có khả năng đặt một **mặt tiền (façade)** dịch vụ của riêng mình ở phía trước đó. Đối với một số tình huống, chúng tôi thậm chí đã sử dụng một `façade` như vậy để trừu tượng hóa các API để truy xuất nội dung.

![alt text](<images/Screenshot from 2025-09-29 06-50-38.png>)

**Hình 4-11.** *Ẩn một CMS bằng dịch vụ của riêng bạn*

Chúng tôi đã sử dụng mẫu hình này nhiều lần trên khắp ThoughtWorks trong vài năm qua, và tôi đã tự mình làm điều này nhiều hơn một lần. Một ví dụ đáng chú ý là một khách hàng đang tìm cách đẩy ra một trang web mới cho các sản phẩm của mình. Ban đầu, họ muốn xây dựng toàn bộ giải pháp trên CMS, nhưng họ vẫn chưa chọn được một cái nào. Thay vào đó, chúng tôi đã đề xuất cách tiếp cận này, và bắt đầu phát triển trang web ở phía trước. Trong khi chờ đợi công cụ CMS được chọn, chúng tôi đã **giả lập (faked)** nó bằng cách có một dịch vụ web chỉ hiển thị nội dung tĩnh. Cuối cùng, chúng tôi đã đưa trang web lên sóng sớm hơn nhiều so với khi CMS được chọn bằng cách sử dụng dịch vụ nội dung giả của chúng tôi trong sản xuất để hiển thị nội dung cho trang web trực tiếp. Sau này, chúng tôi đã có thể chỉ cần thả công cụ được chọn cuối cùng vào mà không có bất kỳ thay đổi nào đối với ứng dụng phía trước.

Sử dụng cách tiếp cận này, chúng tôi giữ phạm vi của những gì CMS làm ở mức tối thiểu và chuyển các tùy chỉnh sang `technology stack` của riêng mình.

##### **Ví dụ: Hệ thống CRM đa vai trò (Example: The multirole CRM system)**

CRM—hay Quản lý Quan hệ Khách hàng—là một con quái vật thường gặp có thể gieo rắc nỗi sợ hãi vào trái tim của ngay cả những kiến trúc sư gan dạ nhất. Lĩnh vực này, được đặc trưng bởi các nhà cung cấp như Salesforce hoặc SAP, đầy rẫy những ví dụ về các công cụ cố gắng làm mọi thứ cho bạn. Điều này có thể dẫn đến việc chính công cụ đó trở thành một điểm lỗi duy nhất, và một mớ hỗn độn các phụ thuộc. Nhiều triển khai các công cụ CRM mà tôi đã thấy là một trong những ví dụ tốt nhất về các dịch vụ **dính (adhesive)** (trái ngược với gắn kết).

Phạm vi của một công cụ như vậy thường bắt đầu nhỏ, nhưng theo thời gian nó trở thành một phần ngày càng quan trọng trong cách tổ chức của bạn hoạt động. Vấn đề là hướng đi và các lựa chọn được đưa ra xung quanh hệ thống quan trọng này thường được đưa ra bởi chính nhà cung cấp công cụ, chứ không phải bạn.

Gần đây, tôi đã tham gia vào một bài tập để cố gắng giành lại một số quyền kiểm soát. Tổ chức mà tôi đang làm việc cùng nhận ra rằng mặc dù họ đang sử dụng công cụ CRM cho rất nhiều thứ, họ không nhận được giá trị từ các chi phí ngày càng tăng liên quan đến nền tảng. Đồng thời, nhiều hệ thống nội bộ đang sử dụng các API CRM không lý tưởng để tích hợp. Chúng tôi muốn chuyển kiến trúc hệ thống theo hướng một nơi mà chúng tôi có các dịch vụ mô hình hóa miền nghiệp vụ của mình, và cũng đặt nền móng cho một sự di chuyển tiềm năng.

Điều đầu tiên chúng tôi làm là xác định các khái niệm cốt lõi cho miền nghiệp vụ của chúng tôi mà hệ thống CRM hiện đang sở hữu. Một trong số đó là khái niệm về một **dự án (project)**—nghĩa là, một cái gì đó mà một nhân viên có thể được giao cho. Nhiều hệ thống khác cần thông tin dự án. Thay vào đó, những gì chúng tôi đã làm là tạo ra một dịch vụ dự án. Dịch vụ này phơi bày các dự án dưới dạng các tài nguyên RESTful, và các hệ thống bên ngoài có thể chuyển các điểm tích hợp của họ sang dịch vụ mới, dễ làm việc hơn. Bên trong, dịch vụ dự án chỉ là một `façade`, che giấu chi tiết của việc tích hợp cơ bản. Bạn có thể thấy điều này trong Hình 4-12.

![alt text](<images/Screenshot from 2025-09-29 06-50-45.png>)

**Hình 4-12.** *Sử dụng các dịch vụ façade để che giấu CRM cơ bản*

Công việc, mà tại thời điểm viết bài này vẫn đang được tiến hành, là xác định các khái niệm miền khác mà CRM đang xử lý, và tạo thêm các `façade` cho chúng. Khi đến lúc di chuyển khỏi CRM cơ bản, chúng tôi sau đó có thể xem xét từng `façade` một để quyết định xem một giải pháp phần mềm nội bộ hay một cái gì đó có sẵn có thể phù hợp hay không.

#### **Mẫu hình Bóp nghẹt (The Strangler Pattern)**

Khi nói đến các nền tảng kế thừa hoặc thậm chí là COTS không hoàn toàn nằm dưới sự kiểm soát của chúng ta, chúng ta cũng phải đối phó với những gì xảy ra khi chúng ta muốn loại bỏ chúng hoặc ít nhất là di chuyển khỏi chúng. Một mẫu hình hữu ích ở đây là **Mẫu hình Ứng dụng Bóp nghẹt (Strangler Application Pattern)**. Giống như ví dụ của chúng ta về việc đặt mã của riêng mình ở phía trước hệ thống CMS, với một `strangler`, bạn bắt và chặn các cuộc gọi đến hệ thống cũ. Điều này cho phép bạn quyết định xem bạn định tuyến các cuộc gọi này đến mã kế thừa hiện có, hay hướng chúng đến mã mới bạn có thể đã viết. Điều này cho phép bạn thay thế chức năng theo thời gian mà không cần phải viết lại toàn bộ.

Khi nói đến microservices, thay vì có một ứng dụng `monolithic` duy nhất chặn tất cả các cuộc gọi đến hệ thống kế thừa hiện có, bạn thay vào đó có thể sử dụng một loạt các microservices để thực hiện việc chặn này. Việc nắm bắt và chuyển hướng các cuộc gọi ban đầu có thể trở nên phức tạp hơn trong tình huống này, và bạn có thể yêu cầu sử dụng một `proxy` để làm điều này cho bạn.

#### **Tóm tắt (Summary)**

Chúng ta đã xem xét một số tùy chọn khác nhau xung quanh việc tích hợp, và tôi đã chia sẻ suy nghĩ của mình về những lựa chọn nào có khả năng đảm bảo nhất rằng các microservice của chúng ta vẫn được tách rời nhất có thể khỏi các cộng tác viên khác của chúng:

*   **Tránh tích hợp cơ sở dữ liệu bằng mọi giá.**
*   **Hiểu các sự đánh đổi giữa REST và RPC, nhưng hãy xem xét mạnh mẽ REST như một điểm khởi đầu tốt cho việc tích hợp `request/response`.**
*   **Ưu tiên vũ đạo hơn điều phối.**
*   **Tránh các thay đổi gây đổ vỡ và nhu cầu phiên bản hóa bằng cách hiểu Luật của Postel và sử dụng các trình đọc khoan dung.**
*   **Hãy nghĩ về các giao diện người dùng như các lớp σύνθεσης.**

Chúng ta đã đề cập khá nhiều ở đây, và không thể đi sâu vào tất cả các chủ đề này. Tuy nhiên, đây nên là một nền tảng tốt để giúp bạn bắt đầu và chỉ cho bạn đi đúng hướng nếu bạn muốn tìm hiểu thêm.

Chúng ta cũng đã dành thời gian xem xét cách làm việc với các hệ thống không hoàn toàn nằm dưới sự kiểm soát của chúng ta dưới dạng các sản phẩm COTS. Hóa ra mô tả này có thể áp dụng dễ dàng cho cả phần mềm chúng ta đã viết!

Một số cách tiếp cận được nêu ở đây áp dụng tốt như nhau cho phần mềm kế thừa, nhưng điều gì sẽ xảy ra nếu chúng ta muốn giải quyết nhiệm vụ thường là hoành tráng là đưa những hệ thống cũ này vào khuôn khổ và phân tách chúng thành các phần có thể sử dụng được hơn? Chúng ta sẽ thảo luận chi tiết về điều đó trong chương tiếp theo.

Tuyệt vời! Chúng ta sẽ bước vào Chương 5: "Chia tách Monolith (Splitting the Monolith)". Chương này sẽ cung cấp những kỹ thuật thực tế để tái cấu trúc một hệ thống lớn thành các microservice nhỏ hơn.

---

### **5. Chia tách Monolith (Splitting the Monolith)**

Chúng ta đã thảo luận về một dịch vụ tốt trông như thế nào, và tại sao các máy chủ nhỏ hơn có thể tốt hơn cho chúng ta. Chúng ta cũng đã thảo luận trước đó về tầm quan trọng của việc có thể phát triển thiết kế của hệ thống của chúng ta. Nhưng làm thế nào để chúng ta xử lý thực tế rằng chúng ta có thể đã có một số lượng lớn các `codebase` đang tồn tại không tuân theo các mẫu này? Làm thế nào để chúng ta phân tách các ứng dụng `monolithic` này mà không cần phải bắt tay vào một cuộc viết lại toàn bộ từ đầu?

`Monolith` phát triển theo thời gian. Nó tiếp thu chức năng mới và các dòng mã với tốc độ đáng báo động. Chẳng bao lâu sau, nó trở thành một sự hiện diện khổng lồ, đáng sợ trong tổ chức của chúng ta mà mọi người đều sợ phải động đến hoặc thay đổi. Nhưng không phải tất cả đều mất! Với các công cụ phù hợp trong tay, chúng ta có thể tiêu diệt con quái vật này.

#### **Tất cả là về các Đường nối (It's All About Seams)**

Chúng ta đã thảo luận trong **Chương 3** rằng chúng ta muốn các dịch vụ của mình có tính **gắn kết cao (highly cohesive)** và **ghép nối lỏng lẻo (loosely coupled)**. Vấn đề với `monolith` là tất cả quá thường xuyên nó lại là điều ngược lại của cả hai. Thay vì có xu hướng hướng tới sự gắn kết, và giữ những thứ có xu hướng thay đổi cùng nhau lại với nhau, chúng ta lại thu thập và dán lại với nhau đủ loại mã không liên quan. Tương tự như vậy, sự ghép nối lỏng lẻo không thực sự tồn tại: nếu tôi muốn thay đổi một dòng mã, tôi có thể làm điều đó khá dễ dàng, nhưng tôi không thể triển khai thay đổi đó mà không có khả năng ảnh hưởng đến phần lớn phần còn lại của `monolith`, và chắc chắn tôi sẽ phải triển khai lại toàn bộ hệ thống.

Trong cuốn sách *Working Effectively with Legacy Code* (Prentice-Hall), Michael Feathers định nghĩa khái niệm về một **đường nối (seam)**—tức là, một phần của mã có thể được xử lý một cách cô lập và làm việc trên đó mà không ảnh hưởng đến phần còn lại của `codebase`. Chúng ta cũng muốn xác định các đường nối. Nhưng thay vì tìm chúng cho mục đích dọn dẹp `codebase` của mình, chúng ta muốn xác định các đường nối có thể trở thành các ranh giới dịch vụ.

Vậy điều gì tạo nên một đường nối tốt? Chà, như chúng ta đã thảo luận trước đó, các **bounded context** tạo nên các đường nối tuyệt vời, bởi vì theo định nghĩa, chúng đại diện cho các ranh giới gắn kết nhưng lại ghép nối lỏng lẻo trong một tổ chức. Vì vậy, bước đầu tiên là bắt đầu xác định các ranh giới này trong mã của chúng ta.

Hầu hết các ngôn ngữ lập trình đều cung cấp các khái niệm không gian tên (`namespace`) cho phép chúng ta nhóm các mã tương tự lại với nhau. Khái niệm `package` của Java là một ví dụ khá yếu, nhưng nó cung cấp cho chúng ta phần lớn những gì chúng ta cần. Tất cả các ngôn ngữ lập trình chính thống khác đều có các khái niệm tương tự được tích hợp sẵn, với JavaScript có thể cho là một ngoại lệ.

#### **Chia nhỏ MusicCorp (Breaking Apart MusicCorp)**

Hãy tưởng tượng chúng ta có một dịch vụ `backend monolithic` lớn đại diện cho một phần đáng kể hành vi của các hệ thống trực tuyến của MusicCorp. Để bắt đầu, chúng ta nên xác định các `bounded context` cấp cao mà chúng ta nghĩ rằng tồn tại trong tổ chức của mình, như chúng ta đã thảo luận trong **Chương 3**. Sau đó, chúng ta muốn cố gắng hiểu `monolith` ánh xạ tới những `bounded context` nào. Hãy tưởng tượng rằng ban đầu chúng ta xác định bốn `context` mà chúng ta nghĩ rằng `backend monolithic` của mình bao gồm:

*   **Catalog (Danh mục)**
    Mọi thứ liên quan đến siêu dữ liệu về các mặt hàng chúng ta cung cấp để bán.
*   **Finance (Tài chính)**
    Báo cáo cho các tài khoản, thanh toán, hoàn tiền, v.v.
*   **Warehouse (Nhà kho)**
    Gửi hàng và trả hàng của khách hàng, quản lý mức tồn kho, v.v.
*   **Recommendation (Đề xuất)**
    Hệ thống đề xuất mang tính cách mạng, đang chờ cấp bằng sáng chế của chúng tôi, là mã rất phức tạp được viết bởi một đội ngũ có nhiều bằng tiến sĩ hơn một phòng thí nghiệm khoa học trung bình.

Điều đầu tiên cần làm là tạo ra các `package` đại diện cho các `context` này, và sau đó di chuyển mã hiện có vào chúng. Với các IDE hiện đại, việc di chuyển mã có thể được thực hiện tự động thông qua các tái cấu trúc (`refactorings`), và có thể được thực hiện từng bước trong khi chúng ta đang làm những việc khác. Tuy nhiên, bạn vẫn sẽ cần các bài kiểm thử để phát hiện bất kỳ sự cố nào do di chuyển mã, đặc biệt nếu bạn đang sử dụng một ngôn ngữ có kiểu động, nơi các IDE gặp khó khăn hơn trong việc thực hiện tái cấu trúc. Theo thời gian, chúng ta bắt đầu thấy mã nào phù hợp và mã nào còn lại và không thực sự phù hợp ở bất cứ đâu. Mã còn lại này thường sẽ xác định các `bounded context` mà chúng ta có thể đã bỏ lỡ!

Trong quá trình này, chúng ta cũng có thể sử dụng mã để phân tích các phụ thuộc giữa các `package` này. Mã của chúng ta nên đại diện cho tổ chức của chúng ta, vì vậy các `package` của chúng ta đại diện cho các `bounded context` trong tổ chức của chúng ta nên tương tác theo cùng một cách mà các nhóm tổ chức trong thế giới thực trong miền nghiệp vụ của chúng ta tương tác. Ví dụ, các công cụ như Structure 101 cho phép chúng ta xem các phụ thuộc giữa các `package` một cách đồ họa. Nếu chúng ta phát hiện ra những điều trông sai—ví dụ, `package` nhà kho phụ thuộc vào mã trong `package` tài chính trong khi không có sự phụ thuộc nào như vậy tồn tại trong tổ chức thực—thì chúng ta có thể điều tra vấn đề này và cố gắng giải quyết nó.

Quá trình này có thể mất một buổi chiều trên một `codebase` nhỏ, hoặc vài tuần hoặc vài tháng khi bạn đang đối phó với hàng triệu dòng mã. Bạn có thể không cần phải sắp xếp tất cả mã vào các `package` hướng miền trước khi tách ra dịch vụ đầu tiên của mình, và thực sự có thể có giá trị hơn nếu tập trung nỗ lực của bạn vào một nơi. Không cần thiết đây phải là một cách tiếp cận "big-bang". Đó là một cái gì đó có thể được thực hiện từng chút một, ngày qua ngày, và chúng ta có rất nhiều công cụ để theo dõi tiến trình của mình.

Vậy bây giờ chúng ta đã tổ chức `codebase` của mình xung quanh các đường nối này, tiếp theo là gì?

#### **Lý do để Chia tách Monolith (The Reasons to Split the Monolith)**

Việc quyết định rằng bạn muốn một dịch vụ hoặc ứng dụng `monolithic` nhỏ hơn là một khởi đầu tốt. Nhưng tôi sẽ khuyên bạn nên đẽo gọt dần các hệ thống này. Một cách tiếp cận từng bước sẽ giúp bạn tìm hiểu về microservices khi bạn đi, và cũng sẽ hạn chế tác động của việc làm sai điều gì đó (và bạn sẽ làm sai điều gì đó!). Hãy nghĩ về `monolith` của chúng ta như một khối đá cẩm thạch. Chúng ta có thể cho nổ tung toàn bộ, nhưng điều đó hiếm khi kết thúc tốt đẹp. Sẽ hợp lý hơn nhiều nếu chỉ đẽo gọt nó một cách từ từ.

Vì vậy, nếu chúng ta định chia nhỏ `monolith` từng mảnh một, chúng ta nên bắt đầu từ đâu? Bây giờ chúng ta đã có các đường nối của mình, nhưng chúng ta nên kéo cái nào ra trước? Tốt nhất là nên suy nghĩ về nơi bạn sẽ nhận được nhiều lợi ích nhất từ việc một phần `codebase` của bạn được tách ra, thay vì chỉ chia tách mọi thứ vì mục đích chia tách. Hãy xem xét một số động lực có thể giúp hướng dẫn cái đục của chúng ta.

##### **Tốc độ Thay đổi (Pace of Change)**

Có lẽ chúng ta biết rằng chúng ta sắp có một loạt các thay đổi về cách chúng ta quản lý hàng tồn kho. Nếu chúng ta tách đường nối nhà kho ra thành một dịch vụ ngay bây giờ, chúng ta có thể thay đổi dịch vụ đó nhanh hơn, vì nó là một đơn vị tự chủ riêng biệt.

##### **Cấu trúc Đội (Team Structure)**

Đội ngũ giao hàng của MusicCorp thực sự được chia thành hai khu vực địa lý. Một đội ở London, đội còn lại ở Hawaii (một số người thật sướng!). Sẽ thật tuyệt nếu chúng ta có thể tách ra mã mà đội Hawaii làm việc nhiều nhất, để họ có thể toàn quyền sở hữu. Chúng ta sẽ khám phá ý tưởng này sâu hơn trong **Chương 10**.

##### **Bảo mật (Security)**

MusicCorp đã có một cuộc kiểm toán bảo mật, và đã quyết định thắt chặt việc bảo vệ thông tin nhạy cảm của mình. Hiện tại, tất cả điều này được xử lý bởi mã liên quan đến tài chính. Nếu chúng ta tách dịch vụ này ra, chúng ta có thể cung cấp các biện pháp bảo vệ bổ sung cho dịch vụ cá nhân này về mặt giám sát, bảo vệ dữ liệu khi truyền và bảo vệ dữ liệu khi nghỉ—những ý tưởng chúng ta sẽ xem xét chi tiết hơn trong **Chương 9**.

##### **Công nghệ (Technology)**

Đội ngũ chăm sóc hệ thống đề xuất của chúng ta đã thử nghiệm một số thuật toán mới sử dụng một thư viện lập trình logic bằng ngôn ngữ Clojure. Đội ngũ nghĩ rằng điều này có thể mang lại lợi ích cho khách hàng của chúng tôi bằng cách cải thiện những gì chúng tôi cung cấp cho họ. Nếu chúng ta có thể tách mã đề xuất ra thành một dịch vụ riêng biệt, sẽ dễ dàng xem xét việc xây dựng một triển khai thay thế mà chúng ta có thể kiểm thử.

##### **Các Phụ thuộc Rối rắm (Tangled Dependencies)**

Một điểm khác cần xem xét khi bạn đã xác định được một vài đường nối để tách ra là mã đó bị vướng víu với phần còn lại của hệ thống như thế nào. Chúng ta muốn kéo ra đường nối ít bị phụ thuộc nhất nếu có thể. Nếu bạn có thể xem các đường nối khác nhau bạn đã tìm thấy như một đồ thị phụ thuộc có hướng không chu trình (directed acyclical graph of dependencies) (một điều mà các công cụ mô hình hóa `package` mà tôi đã đề cập trước đó làm rất tốt), điều này có thể giúp bạn phát hiện ra các đường nối có khả năng khó gỡ rối hơn.

Điều này đưa chúng ta đến cái thường là mẹ của tất cả các phụ thuộc rối rắm: **cơ sở dữ liệu**.

Chắc chắn rồi! Chúng ta sẽ tiếp tục với phần đầy thách thức nhất của việc chia tách một monolith: xử lý cơ sở dữ liệu.

---

#### **Cơ sở dữ liệu (The Database)**

Chúng ta đã thảo luận khá dài về những thách thức của việc sử dụng cơ sở dữ liệu như một phương pháp tích hợp nhiều dịch vụ. Như tôi đã nói khá rõ ràng trước đó, tôi không phải là một người hâm mộ! Điều này có nghĩa là chúng ta cũng cần phải tìm các **đường nối (seams)** trong cơ sở dữ liệu của mình để có thể chia nhỏ chúng một cách sạch sẽ. Tuy nhiên, cơ sở dữ liệu là những con thú khó nhằn.

##### **Nắm bắt Vấn đề (Getting to Grips with the Problem)**

Bước đầu tiên là xem xét chính mã nguồn và xem các phần nào của nó đọc và ghi vào cơ sở dữ liệu. Một thực hành phổ biến là có một lớp `repository`, được hỗ trợ bởi một loại `framework` nào đó như Hibernate, để liên kết mã của bạn với cơ sở dữ liệu, giúp dễ dàng ánh xạ các đối tượng hoặc cấu trúc dữ liệu đến và từ cơ sở dữ liệu. Nếu bạn đã theo dõi cho đến nay, bạn đã nhóm mã của mình vào các `package` đại diện cho các `bounded context` của mình; chúng ta muốn làm điều tương tự cho mã truy cập cơ sở dữ liệu của mình. Điều này có thể yêu cầu chia nhỏ lớp `repository` thành nhiều phần, như được hiển thị trong Hình 5-1.

![alt text](<images/Screenshot from 2025-09-29 06-50-53.png>)   

**Hình 5-1.** *Chia nhỏ các lớp repository của chúng ta*

Việc có mã ánh xạ cơ sở dữ liệu được đặt cùng chỗ bên trong mã cho một `context` nhất định có thể giúp chúng ta hiểu những phần nào của cơ sở dữ liệu được sử dụng bởi những đoạn mã nào. Ví dụ, Hibernate có thể làm điều này rất rõ ràng nếu bạn đang sử dụng một tệp ánh xạ cho mỗi `bounded context`.

Tuy nhiên, điều này không cho chúng ta biết toàn bộ câu chuyện. Ví dụ, chúng ta có thể biết rằng mã tài chính sử dụng bảng sổ cái (`ledger table`), và mã danh mục sử dụng bảng mục hàng (`line item table`), nhưng có thể không rõ ràng rằng cơ sở dữ liệu thực thi một mối quan hệ khóa ngoại từ bảng sổ cái đến bảng mục hàng. Để thấy những ràng buộc cấp cơ sở dữ liệu này, có thể là một trở ngại, chúng ta cần sử dụng một công cụ khác để trực quan hóa dữ liệu. Một nơi tuyệt vời để bắt đầu là sử dụng một công cụ như SchemaSpy miễn phí, có thể tạo ra các biểu diễn đồ họa về các mối quan hệ giữa các bảng.

Tất cả điều này giúp bạn hiểu sự ghép nối giữa các bảng có thể kéo dài qua những gì cuối cùng sẽ trở thành các ranh giới dịch vụ. Nhưng làm thế nào để bạn cắt đứt những mối quan hệ đó? Và còn những trường hợp mà cùng một bảng được sử dụng từ nhiều `bounded context` khác nhau thì sao? Việc xử lý các vấn đề như thế này không dễ dàng, và có nhiều câu trả lời, nhưng nó có thể thực hiện được.

Quay trở lại một số ví dụ cụ thể, hãy xem xét lại cửa hàng âm nhạc của chúng ta. Chúng ta đã xác định được bốn `bounded context`, và muốn tiến tới việc biến chúng thành bốn dịch vụ riêng biệt, hợp tác. Chúng ta sẽ xem xét một vài ví dụ cụ thể về các vấn đề chúng ta có thể gặp phải, và các giải pháp tiềm năng của chúng. Và trong khi một số ví dụ này nói cụ thể về những thách thức gặp phải trong các cơ sở dữ liệu quan hệ tiêu chuẩn, bạn sẽ tìm thấy các vấn đề tương tự trong các kho lưu trữ `NOSQL` thay thế khác.

##### **Ví dụ: Phá vỡ các Mối quan hệ Khóa ngoại (Example: Breaking Foreign Key Relationships)**

Trong ví dụ này, mã danh mục của chúng ta sử dụng một bảng mục hàng chung để lưu trữ thông tin về một album. Mã tài chính của chúng ta sử dụng một bảng sổ cái để theo dõi các giao dịch tài chính. Vào cuối mỗi tháng, chúng ta cần tạo báo cáo cho nhiều người khác nhau trong tổ chức để họ có thể xem chúng ta đang hoạt động như thế nào. Chúng ta muốn làm cho các báo cáo đẹp và dễ đọc, vì vậy thay vì nói, "Chúng ta đã bán 400 bản sao của SKU 12345 và kiếm được 1.300 đô la," chúng ta muốn thêm thông tin về những gì đã được bán (tức là, "Chúng ta đã bán 400 bản sao của *Greatest Hits* của Bruce Springsteen và kiếm được 1.300 đô la"). Để làm điều này, mã báo cáo của chúng ta trong `package` tài chính sẽ truy cập vào bảng mục hàng để lấy ra tiêu đề cho SKU. Nó cũng có thể có một ràng buộc khóa ngoại từ sổ cái đến bảng mục hàng, như chúng ta thấy trong Hình 5-2.

![alt text](<images/Screenshot from 2025-09-29 06-51-02.png>)

**Hình 5-2.** *Mối quan hệ khóa ngoại*

Vậy làm thế nào để chúng ta khắc phục mọi thứ ở đây? Chà, chúng ta cần thực hiện một thay đổi ở hai nơi.
*   **Thứ nhất**, chúng ta cần ngăn mã tài chính truy cập vào bảng mục hàng, vì bảng này thực sự thuộc về mã danh mục, và chúng ta không muốn tích hợp cơ sở dữ liệu xảy ra một khi danh mục và tài chính là các dịch vụ theo đúng nghĩa của chúng.
*   Cách nhanh nhất để giải quyết vấn đề này là thay vì để mã trong tài chính truy cập vào bảng mục hàng, chúng ta sẽ phơi bày dữ liệu thông qua một cuộc gọi API trong `package` danh mục mà mã tài chính có thể gọi. Cuộc gọi API này sẽ là tiền thân của một cuộc gọi mà chúng ta sẽ thực hiện qua dây, như chúng ta thấy trong Hình 5-3.

![alt text](<images/Screenshot from 2025-09-29 06-51-09.png>)

**Hình 5-3.** *Sau khi loại bỏ mối quan hệ khóa ngoại*

Tại thời điểm này, rõ ràng là chúng ta có thể sẽ phải thực hiện hai cuộc gọi cơ sở dữ liệu để tạo báo cáo. Điều này là chính xác. Và điều tương tự sẽ xảy ra nếu đây là hai dịch vụ riêng biệt. Các mối quan tâm về hiệu suất thường được nêu ra. Tôi có một câu trả lời khá dễ dàng cho những người đó: hệ thống của bạn cần nhanh đến mức nào? Và bây giờ nó nhanh đến mức nào? Nếu bạn có thể kiểm tra hiệu suất hiện tại của nó và biết hiệu suất tốt trông như thế nào, thì bạn nên cảm thấy tự tin khi thực hiện một thay đổi. Đôi khi làm cho một thứ chậm hơn để đổi lấy những thứ khác là điều đúng đắn, đặc biệt nếu *chậm hơn* vẫn hoàn toàn chấp nhận được.

Nhưng còn mối quan hệ khóa ngoại thì sao? Chà, chúng ta mất hoàn toàn điều đó. Điều này trở thành một ràng buộc mà bây giờ chúng ta cần phải quản lý trong các dịch vụ kết quả của mình thay vì ở cấp cơ sở dữ liệu. Điều này có thể có nghĩa là chúng ta cần phải thực hiện kiểm tra nhất quán của riêng mình trên các dịch vụ, hoặc kích hoạt các hành động để dọn dẹp dữ liệu liên quan. Liệu điều này có cần thiết hay không thường không phải là lựa chọn của một nhà công nghệ. Ví dụ, nếu dịch vụ đặt hàng của chúng ta chứa một danh sách các ID cho các mục danh mục, điều gì sẽ xảy ra nếu một mục danh mục bị xóa và một đơn đặt hàng bây giờ tham chiếu đến một ID danh mục không hợp lệ? Chúng ta có nên cho phép nó không? Nếu có, thì điều này được thể hiện như thế nào trong đơn đặt hàng khi nó được hiển thị? Nếu không, thì làm thế nào để chúng ta kiểm tra rằng điều này không bị vi phạm? Đây là những câu hỏi bạn sẽ cần được trả lời bởi những người xác định cách hệ thống của bạn nên hoạt động cho người dùng của nó.

##### **Ví dụ: Dữ liệu Tĩnh Dùng chung (Example: Shared Static Data)**

Tôi đã thấy có lẽ có nhiều mã quốc gia được lưu trữ trong cơ sở dữ liệu (được hiển thị trong Hình 5-4) như tôi đã viết các lớp `StringUtils` cho các dự án Java nội bộ. Điều này dường như ngụ ý rằng chúng ta có kế hoạch thay đổi các quốc gia mà hệ thống của chúng ta hỗ trợ thường xuyên hơn nhiều so với việc chúng ta sẽ triển khai mã mới, nhưng dù lý do thực sự là gì, những ví dụ về dữ liệu tĩnh dùng chung được lưu trữ trong cơ sở dữ liệu này xuất hiện rất nhiều. Vậy chúng ta phải làm gì trong cửa hàng âm nhạc của mình nếu tất cả các dịch vụ tiềm năng của chúng ta đều đọc từ cùng một bảng như thế này?

![alt text](<images/Screenshot from 2025-09-29 06-51-16.png>)

**Hình 5-4.** *Mã quốc gia trong cơ sở dữ liệu*

Chà, chúng ta có một vài lựa chọn.
1.  Một là **sao chép** bảng này cho mỗi `package` của chúng ta, với tầm nhìn dài hạn là nó sẽ được sao chép trong mỗi dịch vụ. Điều này tất nhiên dẫn đến một thách thức về tính nhất quán: điều gì sẽ xảy ra nếu tôi cập nhật một bảng để phản ánh việc tạo ra Newmantopia ngoài khơi bờ biển phía đông của Úc, nhưng không phải bảng khác?
2.  Một lựa chọn thứ hai là thay vào đó coi dữ liệu tĩnh dùng chung này như là **mã nguồn**. Có lẽ nó có thể ở trong một tệp thuộc tính được triển khai như một phần của dịch vụ, hoặc có lẽ chỉ là một `enum`. Các vấn đề xung quanh tính nhất quán của dữ liệu vẫn còn, mặc dù kinh nghiệm đã cho thấy rằng việc đẩy ra các thay đổi đối với các tệp cấu hình dễ dàng hơn nhiều so với việc thay đổi các bảng cơ sở dữ liệu trực tiếp. Đây thường là một cách tiếp cận rất hợp lý.
3.  Một lựa chọn thứ ba, có thể là cực đoan, là đẩy dữ liệu tĩnh này vào một **dịch vụ của riêng nó**. Trong một vài tình huống tôi đã gặp, khối lượng, sự phức tạp và các quy tắc liên quan đến dữ liệu tham chiếu tĩnh là đủ để cách tiếp cận này được bảo đảm, nhưng có lẽ là quá mức cần thiết nếu chúng ta chỉ nói về mã quốc gia!

Cá nhân tôi, trong hầu hết các tình huống, tôi sẽ cố gắng thúc đẩy việc giữ dữ liệu này trong các tệp cấu hình hoặc trực tiếp trong mã, vì đó là lựa chọn đơn giản nhất cho hầu hết các trường hợp.

##### **Ví dụ: Dữ liệu Dùng chung (Example: Shared Data)**

Bây giờ hãy đi sâu vào một ví dụ phức tạp hơn, nhưng là một vấn đề phổ biến khi bạn đang cố gắng tách các hệ thống ra: **dữ liệu có thể thay đổi được dùng chung**. Mã tài chính của chúng ta theo dõi các khoản thanh toán được thực hiện bởi khách hàng cho các đơn đặt hàng của họ, và cũng theo dõi các khoản hoàn tiền được trao cho họ khi họ trả lại hàng. Trong khi đó, mã nhà kho cập nhật các bản ghi để cho thấy rằng các đơn đặt hàng cho khách hàng đã được gửi đi hoặc nhận được. Tất cả dữ liệu này được hiển thị ở một nơi thuận tiện trên trang web để khách hàng có thể xem những gì đang diễn ra với tài khoản của họ. Để đơn giản, chúng ta đã lưu trữ tất cả thông tin này trong một bảng hồ sơ khách hàng khá chung chung, như được hiển thị trong Hình 5-5.

![alt text](<images/Screenshot from 2025-09-29 06-51-25.png>)

**Hình 5-5.** *Truy cập dữ liệu khách hàng: chúng ta có đang thiếu một cái gì đó không?*

Vì vậy, cả mã tài chính và nhà kho đều đang ghi vào, và có lẽ thỉnh thoảng đọc từ, cùng một bảng. Làm thế nào chúng ta có thể tách cái này ra? Những gì chúng ta thực sự có ở đây là một cái gì đó bạn sẽ thấy thường xuyên—một khái niệm miền không được mô hình hóa trong mã, và thực tế được mô hình hóa một cách ngầm định trong cơ sở dữ liệu. Ở đây, khái niệm miền bị thiếu là **Khách hàng (Customer)**.

Chúng ta cần làm cho khái niệm trừu tượng hiện tại của khách hàng trở nên cụ thể. Như một bước chuyển tiếp, chúng ta tạo một `package` mới có tên là `Customer`. Sau đó, chúng ta có thể sử dụng một API để phơi bày mã `Customer` cho các `package` khác, chẳng hạn như tài chính hoặc nhà kho. Đẩy tất cả điều này về phía trước, chúng ta bây giờ có thể kết thúc với một dịch vụ khách hàng riêng biệt (Hình 5-6).

![alt text](<images/Screenshot from 2025-09-29 06-51-35.png>)

**Hình 5-6.** *Nhận ra bounded context của khách hàng*

##### **Ví dụ: Các Bảng Dùng chung (Example: Shared Tables)**

Hình 5-7 cho thấy ví dụ cuối cùng của chúng ta. Danh mục của chúng ta cần lưu trữ tên và giá của các bản ghi chúng ta bán, và nhà kho cần giữ một bản ghi điện tử về hàng tồn kho. Chúng ta quyết định giữ hai thứ này ở cùng một nơi trong một bảng mục hàng chung. Trước đây, với tất cả mã được hợp nhất lại với nhau, không rõ ràng rằng chúng ta thực sự đang gộp các mối quan tâm lại với nhau, nhưng bây giờ chúng ta có thể thấy rằng trên thực tế, chúng ta có hai khái niệm riêng biệt có thể được lưu trữ khác nhau.

![alt text](<images/Screenshot from 2025-09-29 06-51-44.png>)

**Hình 5-7.** *Các bảng được chia sẻ giữa các context khác nhau*

Câu trả lời ở đây là **chia bảng thành hai** như chúng ta có trong Hình 5-8, có lẽ tạo ra một bảng danh sách kho cho nhà kho, và một bảng mục danh mục cho các chi tiết danh mục.

![alt text](<images/Screenshot from 2025-09-29 06-51-49.png>)

**Hình 5-8.** *Tách bảng dùng chung ra*

##### **Tái cấu trúc Cơ sở dữ liệu (Refactoring Databases)**

Những gì chúng ta đã đề cập trong các ví dụ trước là một vài tái cấu trúc cơ sở dữ liệu có thể giúp bạn tách các `schema` của mình. Để có một cuộc thảo luận chi tiết hơn về chủ đề này, bạn có thể muốn xem cuốn *Refactoring Databases* của Scott J. Ambler và Pramod J. Sadalage (Addison-Wesley).

Chắc chắn rồi! Chúng ta sẽ tiếp tục với hai phần cuối cùng của Chương 5, tập trung vào việc dàn dựng quá trình chia tách và xử lý các ranh giới giao dịch.

---

#### **Dàn dựng Việc Chia tách (Staging the Break)**

Vậy là chúng ta đã tìm thấy các đường nối (`seams`) trong mã ứng dụng của mình, nhóm nó xung quanh các `bounded context`. Chúng ta đã sử dụng điều này để xác định các đường nối trong cơ sở dữ liệu, và đã cố gắng hết sức để chia nhỏ chúng. Tiếp theo là gì? Bạn có thực hiện một bản phát hành "big-bang", đi từ một dịch vụ `monolithic` với một `schema` duy nhất đến hai dịch vụ, mỗi dịch vụ có `schema` riêng không? Tôi thực sự khuyên bạn nên **chia nhỏ `schema` nhưng vẫn giữ dịch vụ lại với nhau** trước khi chia nhỏ mã ứng dụng thành các microservice riêng biệt, như được hiển thị trong Hình 5-9.

![alt text](<images/Screenshot from 2025-09-29 06-51-57.png>)

**Hình 5-9.** *Dàn dựng việc tách dịch vụ*

Với một `schema` riêng biệt, chúng ta có khả năng sẽ tăng số lượng các cuộc gọi cơ sở dữ liệu để thực hiện một hành động duy nhất. Nơi mà trước đây chúng ta có thể đã có thể có tất cả dữ liệu chúng ta muốn trong một câu lệnh `SELECT` duy nhất, bây giờ chúng ta có thể cần phải lấy dữ liệu trở lại từ hai vị trí và nối chúng trong bộ nhớ. Ngoài ra, chúng ta cuối cùng sẽ phá vỡ tính toàn vẹn giao dịch (`transactional integrity`) khi chúng ta chuyển sang hai `schema`, điều này có thể có tác động đáng kể đến các ứng dụng của chúng ta; chúng ta sẽ thảo luận về điều này tiếp theo. Bằng cách chia nhỏ các `schema` nhưng vẫn giữ mã ứng dụng lại với nhau, chúng ta tự cho mình khả năng hoàn tác các thay đổi của mình hoặc tiếp tục tinh chỉnh mọi thứ mà không ảnh hưởng đến bất kỳ người tiêu dùng nào của dịch vụ của chúng ta. Một khi chúng ta hài lòng rằng việc tách DB là hợp lý, chúng ta sau đó có thể nghĩ đến việc chia nhỏ mã ứng dụng thành hai dịch vụ.

#### **Ranh giới Giao dịch (Transactional Boundaries)**

Giao dịch (`transactions`) là những thứ hữu ích. Chúng cho phép chúng ta nói rằng *những sự kiện này hoặc tất cả đều xảy ra cùng nhau, hoặc không có cái nào xảy ra*. Chúng rất hữu ích khi chúng ta chèn dữ liệu vào cơ sở dữ liệu; chúng cho phép chúng ta cập nhật nhiều bảng cùng một lúc, biết rằng nếu có bất cứ điều gì thất bại, mọi thứ sẽ được hoàn tác (`rolled back`), đảm bảo dữ liệu của chúng ta không rơi vào trạng thái không nhất quán. Nói một cách đơn giản, một giao dịch cho phép chúng ta nhóm lại nhiều hoạt động khác nhau đưa hệ thống của chúng ta từ một trạng thái nhất quán này sang một trạng thái nhất quán khác—mọi thứ hoạt động, hoặc không có gì thay đổi.

Giao dịch không chỉ áp dụng cho cơ sở dữ liệu, mặc dù chúng ta thường sử dụng chúng nhất trong bối cảnh đó. Ví dụ, các `message broker` từ lâu đã cho phép bạn gửi và nhận các thông điệp trong các giao dịch.

Với một `schema monolithic`, tất cả các tạo hoặc cập nhật của chúng ta có lẽ sẽ được thực hiện trong một ranh giới giao dịch duy nhất. Khi chúng ta chia nhỏ cơ sở dữ liệu của mình, chúng ta mất đi sự an toàn được cung cấp bởi việc có một giao dịch duy nhất. Hãy xem xét một ví dụ đơn giản trong bối cảnh MusicCorp. Khi tạo một đơn hàng, tôi muốn cập nhật bảng đơn hàng nói rằng một đơn hàng của khách hàng đã được tạo, và cũng đặt một mục vào một bảng cho đội nhà kho để họ biết có một đơn hàng cần được lấy để gửi đi. Chúng ta đã đi xa đến mức nhóm mã ứng dụng của mình vào các `package` riêng biệt, và cũng đã tách các phần khách hàng và nhà kho của `schema` đủ tốt để chúng ta sẵn sàng đặt chúng vào các `schema` riêng của chúng trước khi tách mã ứng dụng.

Trong một giao dịch duy nhất trong `schema monolithic` hiện tại của chúng ta, việc tạo đơn hàng và chèn bản ghi cho đội nhà kho diễn ra trong một giao dịch duy nhất, như được hiển thị trong Hình 5-10.

![alt text](<images/Screenshot from 2025-09-29 06-52-05.png>)

**Hình 5-10.** *Cập nhật hai bảng trong một giao dịch duy nhất*

Nhưng nếu chúng ta đã tách `schema` thành hai `schema` riêng biệt, một cho dữ liệu liên quan đến khách hàng bao gồm bảng đơn hàng của chúng ta, và một cho nhà kho, chúng ta đã mất đi sự an toàn giao dịch này. Quá trình đặt hàng bây giờ kéo dài qua hai ranh giới giao dịch riêng biệt, như chúng ta thấy trong Hình 5-11. Nếu việc chèn vào bảng đơn hàng của chúng ta thất bại, chúng ta có thể rõ ràng dừng mọi thứ, để lại chúng ta ở một trạng thái nhất quán. Nhưng điều gì sẽ xảy ra khi việc chèn vào bảng đơn hàng hoạt động, nhưng việc chèn vào bảng lấy hàng (`picking table`) thất bại?

![alt text](<images/Screenshot from 2025-09-29 06-52-11.png>)

**Hình 5-11.** *Kéo dài các ranh giới giao dịch cho một hoạt động duy nhất*

##### **Thử lại sau (Try Again Later)**

Thực tế là đơn hàng đã được ghi lại và đặt có thể là đủ đối với chúng ta, và chúng ta có thể quyết định thử lại việc chèn vào bảng lấy hàng của nhà kho vào một ngày sau đó. Chúng ta có thể xếp hàng phần này của hoạt động trong một hàng đợi hoặc tệp nhật ký, và thử lại sau. Đối với một số loại hoạt động, điều này là hợp lý, nhưng chúng ta phải giả định rằng một lần thử lại sẽ khắc phục được nó.

Theo nhiều cách, đây là một hình thức khác của cái được gọi là **nhất quán cuối cùng (eventual consistency)**. Thay vì sử dụng một ranh giới giao dịch để đảm bảo rằng hệ thống ở trong một trạng thái nhất quán khi giao dịch hoàn tất, thay vào đó chúng ta chấp nhận rằng hệ thống sẽ tự đưa mình vào một trạng thái nhất quán vào một thời điểm nào đó trong tương lai. Cách tiếp cận này đặc biệt hữu ích với các hoạt động nghiệp vụ có thể kéo dài. Chúng ta sẽ thảo luận về ý tưởng này sâu hơn trong **Chương 11** khi chúng ta đề cập đến các mẫu hình mở rộng quy mô.

##### **Hủy bỏ Toàn bộ Hoạt động (Abort the Entire Operation)**

Một lựa chọn khác là từ chối toàn bộ hoạt động. Trong trường hợp này, chúng ta phải đưa hệ thống trở lại trạng thái nhất quán. Bảng lấy hàng dễ dàng, vì việc chèn đó đã thất bại, nhưng chúng ta có một giao dịch đã được cam kết trong bảng đơn hàng. Chúng ta cần phải hoàn tác điều này. Những gì chúng ta phải làm là đưa ra một **giao dịch bù trừ (compensating transaction)**, khởi động một giao dịch mới để hoàn tác những gì vừa xảy ra. Đối với chúng ta, đó có thể là một cái gì đó đơn giản như đưa ra một câu lệnh `DELETE` để xóa đơn hàng khỏi cơ sở dữ liệu. Sau đó, chúng ta cũng cần phải báo cáo lại qua giao diện người dùng rằng hoạt động đã thất bại. Ứng dụng của chúng ta có thể xử lý cả hai khía cạnh trong một hệ thống `monolithic`, nhưng chúng ta phải xem xét những gì chúng ta có thể làm khi chúng ta chia nhỏ mã ứng dụng. Liệu logic để xử lý giao dịch bù trừ có nằm trong dịch vụ khách hàng, dịch vụ đặt hàng, hay ở một nơi nào đó khác?

Nhưng điều gì sẽ xảy ra nếu giao dịch bù trừ của chúng ta thất bại? Điều đó chắc chắn có thể xảy ra. Khi đó, chúng ta sẽ có một đơn hàng trong bảng đơn hàng mà không có hướng dẫn lấy hàng tương ứng. Trong tình huống này, bạn sẽ cần phải thử lại giao dịch bù trừ, hoặc cho phép một quy trình `backend` nào đó dọn dẹp sự không nhất quán sau này. Đây có thể là một cái gì đó đơn giản như một màn hình bảo trì mà nhân viên quản trị có quyền truy cập, hoặc một quy trình tự động.

Bây giờ hãy nghĩ về những gì xảy ra nếu chúng ta không có một hoặc hai hoạt động chúng ta muốn nhất quán, mà là ba, bốn, hoặc năm. Việc xử lý các giao dịch bù trừ cho mỗi chế độ lỗi trở nên khá khó khăn để hiểu, chứ đừng nói đến việc triển khai.

##### **Giao dịch Phân tán (Distributed Transactions)**

Một giải pháp thay thế cho việc điều phối các giao dịch bù trừ một cách thủ công là sử dụng một **giao dịch phân tán**. Các giao dịch phân tán cố gắng kéo dài nhiều giao dịch bên trong chúng, sử dụng một quy trình quản lý tổng thể được gọi là **trình quản lý giao dịch (transaction manager)** để điều phối các giao dịch khác nhau được thực hiện bởi các hệ thống cơ bản. Giống như với một giao dịch thông thường, một giao dịch phân tán cố gắng đảm bảo rằng mọi thứ vẫn ở trong một trạng thái nhất quán, chỉ trong trường hợp này nó cố gắng làm như vậy trên nhiều hệ thống khác nhau đang chạy trong các quy trình khác nhau, thường giao tiếp qua các ranh giới mạng.

Thuật toán phổ biến nhất để xử lý các giao dịch phân tán—đặc biệt là các giao dịch ngắn hạn, như trong trường hợp xử lý đơn đặt hàng của khách hàng—là sử dụng **cam kết hai pha (two-phase commit)**. Với cam kết hai pha, đầu tiên là pha bỏ phiếu. Đây là nơi mỗi người tham gia (còn được gọi là một **cohort** trong bối cảnh này) trong giao dịch phân tán nói với trình quản lý giao dịch liệu nó có nghĩ rằng giao dịch cục bộ của nó có thể tiếp tục hay không. Nếu trình quản lý giao dịch nhận được một phiếu *có* từ tất cả những người tham gia, thì nó sẽ yêu cầu tất cả họ tiếp tục và thực hiện các cam kết của họ. Một phiếu *không* duy nhất là đủ để trình quản lý giao dịch gửi đi một lệnh hoàn tác cho tất cả các bên.

Cách tiếp cận này dựa vào việc tất cả các bên tạm dừng cho đến khi quy trình điều phối trung tâm yêu cầu họ tiếp tục. Điều này có nghĩa là chúng ta dễ bị mất điện. Nếu trình quản lý giao dịch bị tắt, các giao dịch đang chờ xử lý sẽ không bao giờ hoàn thành. Nếu một `cohort` không phản hồi trong quá trình bỏ phiếu, mọi thứ sẽ bị chặn. Và cũng có trường hợp xảy ra nếu một cam kết thất bại sau khi bỏ phiếu. Có một giả định ngầm trong thuật toán này rằng điều này không thể xảy ra: nếu một `cohort` nói *có* trong giai đoạn bỏ phiếu, thì chúng ta phải giả định rằng nó sẽ cam kết. Các `cohort` cần một cách để làm cho cam kết này hoạt động vào một thời điểm nào đó. Điều này có nghĩa là thuật toán này không phải là không thể sai lầm—thay vào đó, nó chỉ cố gắng bắt hầu hết các trường hợp lỗi.

Quá trình phối hợp này cũng có nghĩa là **khóa (locks)**; nghĩa là, các giao dịch đang chờ xử lý có thể giữ khóa trên các tài nguyên. Khóa trên các tài nguyên có thể dẫn đến sự tranh chấp, làm cho việc mở rộng quy mô hệ thống trở nên khó khăn hơn nhiều, đặc biệt là trong bối cảnh của các hệ thống phân tán.

Các giao dịch phân tán đã được triển khai cho các `technology stack` cụ thể, chẳng hạn như API Giao dịch của Java, cho phép các tài nguyên khác nhau như cơ sở dữ liệu và hàng đợi thông điệp đều tham gia vào cùng một giao dịch bao trùm. Các thuật toán khác nhau rất khó để làm đúng, vì vậy tôi khuyên bạn nên tránh cố gắng tạo ra thuật toán của riêng mình. Thay vào đó, hãy thực hiện nhiều nghiên cứu về chủ đề này nếu đây có vẻ là con đường bạn muốn đi, và xem liệu bạn có thể sử dụng một triển khai hiện có hay không.

##### **Vậy phải làm gì? (So What to Do?)**

Tất cả các giải pháp này đều thêm sự phức tạp. Như bạn có thể thấy, các giao dịch phân tán rất khó để làm đúng và thực sự có thể cản trở việc mở rộng quy mô. Các hệ thống cuối cùng hội tụ thông qua logic thử lại bù trừ có thể khó lý giải hơn, và có thể cần các hành vi bù trừ khác để khắc phục sự không nhất quán trong dữ liệu.

Khi bạn gặp các hoạt động nghiệp vụ hiện đang xảy ra trong một giao dịch duy nhất, hãy tự hỏi liệu chúng có thực sự cần thiết hay không. Chúng có thể xảy ra trong các giao dịch cục bộ khác nhau, và dựa vào khái niệm về sự nhất quán cuối cùng không? Các hệ thống này dễ xây dựng và mở rộng quy mô hơn nhiều (chúng ta sẽ thảo luận nhiều hơn về điều này trong **Chương 11**).

Nếu bạn gặp trạng thái thực sự, thực sự muốn được giữ nhất quán, hãy làm mọi thứ bạn có thể để tránh chia nhỏ nó ngay từ đầu. *Hãy cố gắng hết sức*. Nếu bạn thực sự cần phải tiếp tục với việc chia tách, hãy nghĩ đến việc chuyển từ một cái nhìn hoàn toàn kỹ thuật về quy trình (ví dụ, một giao dịch cơ sở dữ liệu) và thực sự tạo ra một khái niệm cụ thể để đại diện cho chính giao dịch đó. Điều này cung cấp cho bạn một cái móc, hoặc một điểm neo, để chạy các hoạt động khác như các giao dịch bù trừ, và một cách để giám sát và quản lý các khái niệm phức tạp hơn này trong hệ thống của bạn. Ví dụ, bạn có thể tạo ra ý tưởng về một "đơn hàng đang xử lý" cung cấp cho bạn một nơi tự nhiên để tập trung tất cả logic xung quanh việc xử lý đơn hàng từ đầu đến cuối (và xử lý các ngoại lệ).

*(Phần cuối cùng của chương này sẽ đề cập đến một thách thức lớn khác khi chia nhỏ monolith: "Reporting".)*

Tuyệt vời! Chúng ta sẽ đi tiếp với phần cuối cùng của Chương 5, tập trung vào một thách thức quan trọng khác khi chia nhỏ monolith: "Báo cáo (Reporting)".

---

#### **Báo cáo (Reporting)**

Như chúng ta đã thấy, trong việc chia nhỏ một dịch vụ thành các phần nhỏ hơn, chúng ta cũng cần phải có khả năng chia nhỏ cách thức và nơi dữ liệu được lưu trữ. Tuy nhiên, điều này tạo ra một vấn đề khi nói đến một trường hợp sử dụng quan trọng và phổ biến: **báo cáo (reporting)**.

Một sự thay đổi kiến trúc cơ bản như chuyển sang kiến trúc microservices sẽ gây ra rất nhiều sự gián đoạn, nhưng điều đó không có nghĩa là chúng ta phải từ bỏ mọi thứ chúng ta làm. Đối tượng của các hệ thống báo cáo của chúng ta là những người dùng như bất kỳ ai khác, và chúng ta cần xem xét nhu cầu của họ. Sẽ là kiêu ngạo nếu thay đổi hoàn toàn kiến trúc của chúng ta và chỉ yêu cầu họ thích nghi. Mặc dù tôi không đề nghị rằng không gian báo cáo không chín muồi cho sự đột phá—chắc chắn là có—nhưng có giá trị trong việc xác định cách làm việc với các quy trình hiện có trước tiên. Đôi khi chúng ta phải chọn những trận chiến của mình.

##### **Cơ sở dữ liệu Báo cáo (The Reporting Database)**

Báo cáo thường cần nhóm dữ liệu từ nhiều bộ phận khác nhau của tổ chức của chúng ta để tạo ra kết quả hữu ích. Ví dụ, chúng ta có thể muốn làm phong phú dữ liệu từ sổ cái chung của mình bằng các mô tả về những gì đã được bán, mà chúng ta lấy từ một danh mục. Hoặc chúng ta có thể muốn xem xét hành vi mua sắm của các khách hàng có giá trị cao cụ thể, điều này có thể yêu cầu thông tin từ lịch sử mua hàng và hồ sơ khách hàng của họ.

Trong một kiến trúc dịch vụ `monolithic` tiêu chuẩn, tất cả dữ liệu của chúng ta được lưu trữ trong một cơ sở dữ liệu lớn. Điều này có nghĩa là tất cả dữ liệu đều ở một nơi, vì vậy việc báo cáo trên tất cả thông tin thực sự khá dễ dàng, vì chúng ta có thể chỉ cần nối (`join`) qua dữ liệu thông qua các truy vấn SQL hoặc tương tự. Thông thường, chúng ta sẽ không chạy các báo cáo này trên cơ sở dữ liệu chính vì sợ tải trọng được tạo ra bởi các truy vấn của chúng ta ảnh hưởng đến hiệu suất của hệ thống chính, vì vậy thường các hệ thống báo cáo này được treo trên một bản sao chỉ đọc (`read replica`) như được hiển thị trong Hình 5-12.

![alt text](<images/Screenshot from 2025-09-29 06-52-20.png>)

**Hình 5-12.** *Sao chép đọc tiêu chuẩn*

Với cách tiếp cận này, chúng ta có một ưu điểm đáng kể—tất cả dữ liệu đã ở một nơi, vì vậy chúng ta có thể sử dụng các công cụ khá đơn giản để truy vấn nó. Nhưng cũng có một vài nhược điểm với cách tiếp cận này.
*   **Thứ nhất**, `schema` của cơ sở dữ liệu giờ đây thực chất là một API được chia sẻ giữa các dịch vụ `monolithic` đang chạy và bất kỳ hệ thống báo cáo nào. Vì vậy, một thay đổi trong `schema` phải được quản lý cẩn thận. Trong thực tế, đây là một trở ngại khác làm giảm cơ hội bất kỳ ai muốn đảm nhận nhiệm vụ thực hiện và phối hợp một thay đổi như vậy.
*   **Thứ hai**, chúng ta có các tùy chọn hạn chế về cách cơ sở dữ liệu có thể được tối ưu hóa cho một trong hai trường hợp sử dụng—hỗ trợ hệ thống trực tiếp hoặc hệ thống báo cáo. Một số cơ sở dữ liệu cho phép chúng ta thực hiện các tối ưu hóa trên các bản sao chỉ đọc để cho phép báo cáo nhanh hơn, hiệu quả hơn; ví dụ, MySQL sẽ cho phép chúng ta chạy một `backend` khác không có chi phí quản lý các giao dịch. Tuy nhiên, chúng ta không thể cấu trúc dữ liệu khác đi để làm cho báo cáo nhanh hơn nếu sự thay đổi trong cấu trúc dữ liệu đó có tác động xấu đến hệ thống đang chạy. Điều thường xảy ra là `schema` cuối cùng trở nên tuyệt vời cho một trường hợp sử dụng và tệ hại cho trường hợp còn lại, hoặc trở thành mẫu số chung thấp nhất, không tuyệt vời cho mục đích nào cả.
*   **Cuối cùng**, các tùy chọn cơ sở dữ liệu có sẵn cho chúng ta đã bùng nổ gần đây. Trong khi các cơ sở dữ liệu quan hệ tiêu chuẩn phơi bày các giao diện truy vấn SQL hoạt động với nhiều công cụ báo cáo, chúng không phải lúc nào cũng là lựa chọn tốt nhất để lưu trữ dữ liệu cho các dịch vụ đang chạy của chúng ta.

Điều gì sẽ xảy ra nếu dữ liệu ứng dụng của chúng ta được mô hình hóa tốt hơn dưới dạng đồ thị, như trong Neo4j? Hoặc nếu chúng ta muốn sử dụng một kho tài liệu như MongoDB? Tương tự như vậy, điều gì sẽ xảy ra nếu chúng ta muốn khám phá việc sử dụng một cơ sở dữ liệu hướng cột như Cassandra cho hệ thống báo cáo của mình, điều này giúp việc mở rộng quy mô cho các khối lượng lớn hơn trở nên dễ dàng hơn nhiều? Việc bị hạn chế phải có một cơ sở dữ liệu cho cả hai mục đích dẫn đến việc chúng ta thường không thể đưa ra những lựa chọn này và khám phá các tùy chọn mới.

Vì vậy, nó không hoàn hảo, nhưng nó hoạt động (hầu hết). Bây giờ nếu thông tin của chúng ta được lưu trữ trong nhiều hệ thống khác nhau, chúng ta phải làm gì? Có cách nào để chúng ta tập hợp tất cả dữ liệu lại với nhau để chạy các báo cáo của mình không? Và liệu chúng ta có thể tìm ra một cách để loại bỏ một số nhược điểm liên quan đến mô hình cơ sở dữ liệu báo cáo tiêu chuẩn không?

Hóa ra chúng ta có một số giải pháp thay thế khả thi cho cách tiếp cận này. Giải pháp nào có ý nghĩa nhất đối với bạn sẽ phụ thuộc vào một số yếu tố, nhưng chúng ta sẽ khám phá một vài tùy chọn khác nhau mà tôi đã thấy trong thực tế.

##### **Truy xuất Dữ liệu qua các Cuộc gọi Dịch vụ (Data Retrieval via Service Calls)**

Có nhiều biến thể của mô hình này, nhưng tất cả chúng đều dựa vào việc kéo dữ liệu cần thiết từ các hệ thống nguồn thông qua các cuộc gọi API. Đối với một hệ thống báo cáo rất đơn giản, như một bảng điều khiển có thể chỉ muốn hiển thị số lượng đơn đặt hàng được đặt trong 15 phút qua, điều này có thể ổn. Để báo cáo trên dữ liệu từ hai hoặc nhiều hệ thống, bạn cần thực hiện nhiều cuộc gọi để lắp ráp dữ liệu này.

Tuy nhiên, cách tiếp cận này nhanh chóng bị phá vỡ với các trường hợp sử dụng yêu cầu khối lượng dữ liệu lớn hơn. Hãy tưởng tượng một trường hợp sử dụng nơi chúng ta muốn báo cáo về hành vi mua hàng của khách hàng cho cửa hàng âm nhạc của chúng ta trong 24 tháng qua, xem xét các xu hướng khác nhau trong hành vi của khách hàng và điều này đã tác động đến doanh thu như thế nào. Chúng ta cần kéo khối lượng lớn dữ liệu từ ít nhất là các hệ thống khách hàng và tài chính. Việc giữ một bản sao cục bộ của dữ liệu này trong hệ thống báo cáo là nguy hiểm, vì chúng ta có thể không biết liệu nó có thay đổi hay không (ngay cả dữ liệu lịch sử cũng có thể bị thay đổi sau đó), vì vậy để tạo ra một báo cáo chính xác, chúng ta cần tất cả các bản ghi tài chính và khách hàng trong hai năm qua. Với ngay cả số lượng khách hàng khiêm tốn, bạn có thể thấy rằng điều này nhanh chóng sẽ trở thành một hoạt động rất chậm.

Các hệ thống báo cáo cũng thường dựa vào các công cụ của bên thứ ba mong đợi truy xuất dữ liệu theo một cách nhất định, và ở đây việc cung cấp một giao diện SQL là cách nhanh nhất để đảm bảo chuỗi công cụ báo cáo của bạn dễ tích hợp nhất có thể. Tất nhiên, chúng ta vẫn có thể sử dụng cách tiếp cận này để kéo dữ liệu định kỳ vào một cơ sở dữ liệu SQL, nhưng điều này vẫn đặt ra cho chúng ta một số thách thức.

Một trong những thách thức chính là các API được phơi bày bởi các microservice khác nhau có thể không được thiết kế cho các trường hợp sử dụng báo cáo. Ví dụ, một dịch vụ khách hàng có thể cho phép chúng ta tìm một khách hàng bằng ID, hoặc tìm kiếm một khách hàng bằng các trường khác nhau, nhưng không nhất thiết phải phơi bày một API để truy xuất tất cả khách hàng. Điều này có thể dẫn đến nhiều cuộc gọi được thực hiện để truy xuất tất cả dữ liệu—ví dụ, phải lặp qua một danh sách tất cả các khách hàng, thực hiện một cuộc gọi riêng cho mỗi người. Điều này không chỉ có thể không hiệu quả đối với hệ thống báo cáo, mà nó còn có thể tạo ra tải trọng cho dịch vụ được đề cập.

Mặc dù chúng ta có thể tăng tốc một số việc truy xuất dữ liệu bằng cách thêm các tiêu đề bộ nhớ đệm vào các tài nguyên được phơi bày bởi dịch vụ của chúng ta, và để dữ liệu này được lưu vào bộ nhớ đệm trong một cái gì đó như một `reverse proxy`, bản chất của báo cáo thường là chúng ta truy cập vào **đuôi dài (long tail)** của dữ liệu. Điều này có nghĩa là chúng ta có thể yêu cầu các tài nguyên mà không ai khác đã yêu cầu trước đây (hoặc ít nhất là không trong một thời gian đủ dài), dẫn đến một lần bỏ lỡ bộ nhớ đệm (`cache miss`) có khả năng tốn kém.

Bạn có thể giải quyết vấn đề này bằng cách phơi bày các API hàng loạt để làm cho việc báo cáo dễ dàng hơn. Ví dụ, dịch vụ khách hàng của chúng ta có thể cho phép bạn truyền vào một danh sách các ID khách hàng để truy xuất chúng theo lô, hoặc thậm chí có thể phơi bày một giao diện cho phép bạn phân trang qua tất cả các khách hàng. Một phiên bản cực đoan hơn của điều này là mô hình hóa yêu cầu hàng loạt như một tài nguyên theo đúng nghĩa của nó. Ví dụ, dịch vụ khách hàng có thể phơi bày một cái gì đó như một điểm cuối tài nguyên `BatchCustomerExport`. Hệ thống gọi sẽ `POST` một `BatchRequest`, có lẽ truyền vào một vị trí nơi một tệp có thể được đặt với tất cả dữ liệu. Dịch vụ khách hàng sẽ trả về một mã phản hồi `HTTP 202`, cho biết rằng yêu cầu đã được chấp nhận nhưng chưa được xử lý. Hệ thống gọi sau đó có thể thăm dò tài nguyên đang chờ cho đến khi nó truy xuất được trạng thái `201 Created`, cho biết rằng yêu cầu đã được hoàn thành, và sau đó hệ thống gọi có thể đi và lấy dữ liệu. Điều này sẽ cho phép các tệp dữ liệu có khả năng lớn được xuất mà không có chi phí gửi qua `HTTP`; thay vào đó, hệ thống có thể chỉ cần lưu một tệp CSV vào một vị trí được chia sẻ.

Tôi đã thấy cách tiếp cận trước đây được sử dụng để chèn dữ liệu hàng loạt, nơi nó hoạt động tốt. Tuy nhiên, tôi ít ủng hộ nó cho các hệ thống báo cáo, vì tôi cảm thấy rằng có những giải pháp khác, có khả năng đơn giản hơn có thể mở rộng quy mô hiệu quả hơn khi bạn đang đối phó với các nhu cầu báo cáo truyền thống.

##### **Bơm Dữ liệu (Data Pumps)**

Thay vì để hệ thống báo cáo kéo dữ liệu, chúng ta thay vào đó có thể để dữ liệu được đẩy đến hệ thống báo cáo. Một trong những nhược điểm của việc truy xuất dữ liệu bằng các cuộc gọi `HTTP` tiêu chuẩn là chi phí của `HTTP` khi chúng ta thực hiện một số lượng lớn các cuộc gọi, cùng với chi phí của việc phải tạo ra các API có thể chỉ tồn tại cho các mục đích báo cáo. Một tùy chọn thay thế là có một chương trình độc lập truy cập trực tiếp vào cơ sở dữ liệu của dịch vụ là nguồn dữ liệu, và bơm nó vào một cơ sở dữ liệu báo cáo, như được hiển thị trong Hình 5-13.

![alt text](<images/Screenshot from 2025-09-29 06-52-28.png>)

**Hình 5-13.** *Sử dụng một bơm dữ liệu để đẩy dữ liệu định kỳ đến một cơ sở dữ liệu báo cáo trung tâm*

Tại thời điểm này, bạn sẽ nói, "Nhưng Sam, bạn đã nói rằng việc có nhiều chương trình tích hợp trên cùng một cơ sở dữ liệu là một ý tưởng tồi!" Ít nhất tôi hy vọng bạn sẽ nói như vậy, với sự kiên quyết của tôi khi đưa ra quan điểm đó trước đó! Cách tiếp cận này, nếu được triển khai đúng cách, là một ngoại lệ đáng chú ý, nơi những nhược điểm của sự ghép nối được giảm thiểu nhiều hơn bởi việc làm cho báo cáo dễ dàng hơn.

Để bắt đầu, **bơm dữ liệu (`data pump`)** nên được xây dựng và quản lý bởi cùng một đội ngũ quản lý dịch vụ. Đây có thể là một cái gì đó đơn giản như một chương trình dòng lệnh được kích hoạt thông qua Cron. Chương trình này cần phải có kiến thức sâu sắc về cả cơ sở dữ liệu nội bộ cho dịch vụ, và cả `schema` báo cáo. Công việc của bơm là ánh xạ từ cái này sang cái kia. Chúng ta cố gắng giảm bớt các vấn đề với việc ghép nối với `schema` của dịch vụ bằng cách để cùng một đội ngũ quản lý dịch vụ cũng quản lý bơm. Trên thực tế, tôi sẽ đề nghị rằng bạn kiểm soát phiên bản chúng cùng nhau, và có các bản dựng của bơm dữ liệu được tạo ra như một tạo tác bổ sung như một phần của bản dựng của chính dịch vụ, với giả định rằng bất cứ khi nào bạn triển khai một trong số chúng, bạn sẽ triển khai cả hai. Khi chúng ta tuyên bố rõ ràng rằng chúng ta triển khai chúng cùng nhau, và không mở quyền truy cập vào `schema` cho bất kỳ ai bên ngoài đội dịch vụ, nhiều thách thức tích hợp DB truyền thống phần lớn được giảm thiểu.

Sự ghép nối trên chính `schema` báo cáo vẫn còn, nhưng chúng ta phải coi nó như một API được xuất bản khó thay đổi. Một số cơ sở dữ liệu cung cấp cho chúng ta các kỹ thuật nơi chúng ta có thể giảm thiểu thêm chi phí này. Hình 5-14 cho thấy một ví dụ về điều này cho các cơ sở dữ liệu quan hệ, nơi chúng ta có thể có một `schema` trong cơ sở dữ liệu báo cáo cho mỗi dịch vụ, sử dụng những thứ như các **khung nhìn cụ thể hóa (materialized views)** để tạo ra khung nhìn tổng hợp. Bằng cách đó, chúng ta chỉ phơi bày `schema` báo cáo cho dữ liệu khách hàng cho bơm dữ liệu khách hàng. Liệu đây có phải là điều bạn có thể làm một cách hiệu quả hay không, tuy nhiên, sẽ phụ thuộc vào các khả năng của cơ sở dữ liệu bạn đã chọn để báo cáo.

![alt text](<images/Screenshot from 2025-09-29 06-52-51.png>)

**Hình 5-14.** *Sử dụng các khung nhìn cụ thể hóa để tạo thành một schema báo cáo monolithic duy nhất*

Tất nhiên, ở đây, sự phức tạp của việc tích hợp được đẩy sâu hơn vào `schema`, và sẽ dựa vào các khả năng trong cơ sở dữ liệu để làm cho một thiết lập như vậy hoạt động hiệu quả. Mặc dù tôi nghĩ rằng các bơm dữ liệu nói chung là một gợi ý hợp lý và khả thi, tôi ít bị thuyết phục hơn rằng sự phức tạp của một `schema` phân đoạn là đáng giá, đặc biệt là với những thách thức trong việc quản lý sự thay đổi trong cơ sở dữ liệu.

##### **Các Đích đến Thay thế (Alternative Destinations)**

Trên một dự án tôi đã tham gia, chúng tôi đã sử dụng một loạt các bơm dữ liệu để điền các tệp JSON trong AWS S3, thực chất sử dụng S3 để giả dạng như một kho dữ liệu khổng lồ! Cách tiếp cận này hoạt động rất tốt cho đến khi chúng tôi cần mở rộng quy mô giải pháp của mình, và tại thời điểm viết bài này, chúng tôi đang tìm cách thay đổi các bơm này để thay vào đó điền vào một khối lập phương có thể được tích hợp với các công cụ báo cáo tiêu chuẩn như Excel và Tableau.

##### **Bơm Dữ liệu Sự kiện (Event Data Pump)**

Trong **Chương 4**, chúng ta đã đề cập đến ý tưởng về các microservice phát ra các sự kiện dựa trên sự thay đổi trạng thái của các thực thể mà chúng quản lý. Ví dụ, dịch vụ khách hàng của chúng ta có thể phát ra một sự kiện khi một khách hàng nhất định được tạo, hoặc cập nhật, hoặc xóa. Đối với những microservice phơi bày các nguồn cấp sự kiện như vậy, chúng ta có tùy chọn viết người đăng ký sự kiện của riêng mình để bơm dữ liệu vào cơ sở dữ liệu báo cáo, như được hiển thị trong Hình 5-15.

![alt text](<images/Screenshot from 2025-09-29 06-53-01.png>)

**Hình 5-15.** *Một bơm dữ liệu sự kiện sử dụng các sự kiện thay đổi trạng thái để điền vào một cơ sở dữ liệu báo cáo*

Sự ghép nối trên cơ sở dữ liệu cơ bản của microservice nguồn bây giờ đã được tránh. Thay vào đó, chúng ta chỉ đang ràng buộc với các sự kiện được phát ra bởi dịch vụ, được thiết kế để được phơi bày cho người tiêu dùng bên ngoài. Với việc các sự kiện có bản chất thời gian, nó cũng giúp chúng ta thông minh hơn trong việc dữ liệu nào chúng ta gửi đến kho báo cáo trung tâm của mình; chúng ta có thể gửi dữ liệu đến hệ thống báo cáo khi chúng ta thấy một sự kiện, cho phép dữ liệu chảy nhanh hơn đến hệ thống báo cáo của chúng ta, thay vì dựa vào một lịch trình thường xuyên như với bơm dữ liệu.

Ngoài ra, nếu chúng ta lưu trữ những sự kiện nào đã được xử lý, chúng ta có thể chỉ cần xử lý các sự kiện mới khi chúng đến, giả sử các sự kiện cũ đã được ánh xạ vào hệ thống báo cáo. Điều này có nghĩa là việc chèn của chúng ta sẽ hiệu quả hơn, vì chúng ta chỉ cần gửi các **delta (thay đổi)**. Chúng ta có thể làm những điều tương tự với một bơm dữ liệu, nhưng chúng ta phải tự quản lý điều này, trong khi bản chất thời gian cơ bản của luồng sự kiện (x xảy ra tại dấu thời gian y) giúp chúng ta rất nhiều.

Vì bơm dữ liệu sự kiện của chúng ta ít được ghép nối hơn với các phần nội bộ của dịch vụ, cũng dễ dàng hơn để xem xét việc này được quản lý bởi một nhóm riêng biệt với đội ngũ chăm sóc chính microservice. Miễn là bản chất của luồng sự kiện của chúng ta không ghép nối quá mức các người đăng ký với các thay đổi trong dịch vụ, bộ ánh xạ sự kiện này có thể phát triển độc lập với dịch vụ mà nó đăng ký.

Những nhược điểm chính của cách tiếp cận này là tất cả các thông tin cần thiết phải được phát đi dưới dạng các sự kiện, và nó có thể không mở rộng quy mô tốt như một bơm dữ liệu cho các khối lượng dữ liệu lớn hơn có lợi ích của việc hoạt động trực tiếp ở cấp cơ sở dữ liệu. Tuy nhiên, sự ghép nối lỏng lẻo hơn và dữ liệu tươi hơn có sẵn thông qua một cách tiếp cận như vậy làm cho nó đáng được xem xét mạnh mẽ nếu bạn đã phơi bày các sự kiện phù hợp.

##### **Bơm Dữ liệu Sao lưu (Backup Data Pump)**

Tùy chọn này dựa trên một cách tiếp cận được sử dụng tại Netflix, tận dụng các giải pháp sao lưu hiện có và cũng giải quyết một số vấn đề về quy mô mà Netflix phải đối phó. Theo một số cách, bạn có thể coi đây là một trường hợp đặc biệt của bơm dữ liệu, nhưng nó có vẻ như một giải pháp thú vị đến mức nó xứng đáng được đưa vào.

Netflix đã quyết định chuẩn hóa trên Cassandra làm kho lưu trữ nền tảng cho các dịch vụ của mình, trong đó có rất nhiều. Netflix đã đầu tư thời gian đáng kể vào việc xây dựng các công cụ để làm cho Cassandra dễ làm việc, phần lớn trong số đó công ty đã chia sẻ với phần còn lại của thế giới thông qua nhiều dự án mã nguồn mở. Rõ ràng là rất quan trọng để dữ liệu mà Netflix lưu trữ được sao lưu đúng cách. Để sao lưu dữ liệu Cassandra, cách tiếp cận tiêu chuẩn là tạo một bản sao của các tệp dữ liệu hỗ trợ nó và lưu trữ chúng ở một nơi nào đó an toàn. Netflix lưu trữ các tệp này, được gọi là SSTables, trong kho lưu trữ đối tượng S3 của Amazon, cung cấp các đảm bảo về độ bền dữ liệu đáng kể.

Netflix cần báo cáo trên tất cả dữ liệu này, nhưng với quy mô liên quan, đây là một thách thức không tầm thường. Cách tiếp cận của họ là sử dụng Hadoop sử dụng sao lưu SSTable làm nguồn cho các công việc của mình. Cuối cùng, Netflix đã kết thúc việc triển khai một đường ống có khả năng xử lý khối lượng lớn dữ liệu bằng cách sử dụng cách tiếp cận này, mà sau đó họ đã mã nguồn mở dưới dạng dự án Aegisthus. Tuy nhiên, giống như các bơm dữ liệu, với mẫu hình này, chúng ta vẫn có một sự ghép nối với `schema` báo cáo đích (hoặc hệ thống đích).

Có thể hình dung rằng việc sử dụng một cách tiếp cận tương tự—nghĩa là, sử dụng các bộ ánh xạ hoạt động trên các bản sao lưu—sẽ hoạt động trong các bối cảnh khác. Và nếu bạn đã sử dụng Cassandra, Netflix đã làm phần lớn công việc cho bạn!

##### **Hướng tới Thời gian thực (Toward Real Time)**

Nhiều mẫu hình được nêu trước đây là các cách khác nhau để lấy rất nhiều dữ liệu từ nhiều nơi khác nhau đến một nơi. Nhưng liệu ý tưởng rằng tất cả các báo cáo của chúng ta sẽ được thực hiện từ một vị trí có thực sự còn đứng vững nữa không? Chúng ta có các bảng điều khiển, cảnh báo, báo cáo tài chính, phân tích người dùng—tất cả các trường hợp sử dụng này đều có các mức độ chịu đựng khác nhau về độ chính xác và tính kịp thời, điều này có thể dẫn đến các tùy chọn kỹ thuật khác nhau được đưa ra. Như tôi sẽ chi tiết trong **Chương 8**, chúng ta đang ngày càng hướng tới các hệ thống sự kiện chung có khả năng định tuyến dữ liệu của chúng ta đến nhiều nơi khác nhau tùy thuộc vào nhu cầu.

*(Phần tiếp theo và cuối cùng của chương này sẽ thảo luận về "Cost of Change" và "Understanding Root Causes".)*

Chắc chắn rồi! Chúng ta sẽ hoàn thành Chương 5 với hai phần cuối cùng, tập trung vào chi phí của sự thay đổi và việc hiểu rõ nguyên nhân gốc rễ của các vấn đề.

---

#### **Chi phí Thay đổi (Cost of Change)**

Có nhiều lý do tại sao, trong suốt cuốn sách này, tôi thúc đẩy nhu cầu thực hiện những thay đổi nhỏ, tăng dần, nhưng một trong những động lực chính là để hiểu tác động của mỗi sự thay đổi chúng ta thực hiện và thay đổi hướng đi nếu cần. Điều này cho phép chúng ta giảm thiểu tốt hơn chi phí của những sai lầm, nhưng không loại bỏ hoàn toàn khả năng mắc sai lầm. Chúng ta có thể—và sẽ—mắc sai lầm, và chúng ta nên chấp nhận điều đó. Tuy nhiên, điều chúng ta cũng nên làm là hiểu cách tốt nhất để giảm thiểu chi phí của những sai lầm đó.

Như chúng ta đã thấy, chi phí liên quan đến việc di chuyển mã xung quanh trong một `codebase` là khá nhỏ. Chúng ta có rất nhiều công cụ hỗ trợ chúng ta, và nếu chúng ta gây ra một vấn đề, việc sửa chữa thường nhanh chóng. Tuy nhiên, việc chia nhỏ một cơ sở dữ liệu là công việc nhiều hơn rất nhiều, và việc hoàn tác một thay đổi cơ sở dữ liệu cũng phức tạp không kém. Tương tự như vậy, việc gỡ rối một sự tích hợp quá phụ thuộc giữa các dịch vụ, hoặc phải viết lại hoàn toàn một API được sử dụng bởi nhiều người tiêu dùng, có thể là một công việc đáng kể. Chi phí thay đổi lớn có nghĩa là những hoạt động này ngày càng rủi ro hơn. Làm thế nào chúng ta có thể quản lý rủi ro này? Cách tiếp cận của tôi là cố gắng mắc sai lầm ở nơi có tác động thấp nhất.

Tôi có xu hướng thực hiện phần lớn suy nghĩ của mình ở nơi có chi phí thay đổi và chi phí sai lầm thấp nhất có thể: **bảng trắng**. Hãy phác thảo thiết kế đề xuất của bạn. Xem điều gì sẽ xảy ra khi bạn chạy các trường hợp sử dụng qua những gì bạn nghĩ là ranh giới dịch vụ của mình. Đối với cửa hàng âm nhạc của chúng ta, chẳng hạn, hãy tưởng tượng điều gì sẽ xảy ra khi một khách hàng tìm kiếm một bản ghi, đăng ký với trang web, hoặc mua một album. Những cuộc gọi nào được thực hiện? Bạn có bắt đầu thấy các tham chiếu vòng tròn kỳ lạ không? Bạn có thấy hai dịch vụ quá "lắm lời" (`overly chatty`), điều này có thể chỉ ra rằng chúng nên là một thứ không?

Một kỹ thuật tuyệt vời ở đây là điều chỉnh một cách tiếp cận thường được dạy cho việc thiết kế các hệ thống hướng đối tượng: **thẻ lớp-trách nhiệm-cộng tác (class-responsibility-collaboration - CRC)**. Với thẻ CRC, bạn viết trên một thẻ chỉ mục tên của lớp, trách nhiệm của nó là gì, và nó cộng tác với ai. Khi làm việc thông qua một thiết kế được đề xuất, đối với mỗi dịch vụ, tôi liệt kê các trách nhiệm của nó về các khả năng mà nó cung cấp, với các cộng tác viên được chỉ định trong sơ đồ. Khi bạn làm việc qua nhiều trường hợp sử dụng hơn, bạn bắt đầu có cảm giác về việc liệu tất cả những điều này có ăn khớp với nhau một cách đúng đắn hay không.

#### **Hiểu Nguyên nhân Gốc rễ (Understanding Root Causes)**

Chúng ta đã thảo luận về cách chia nhỏ các dịch vụ lớn hơn thành các dịch vụ nhỏ hơn, nhưng tại sao những dịch vụ này lại phát triển quá lớn ngay từ đầu? Điều đầu tiên cần hiểu là việc phát triển một dịch vụ đến mức cần phải chia nhỏ là hoàn toàn **ỔN**. Chúng ta muốn kiến trúc của hệ thống thay đổi theo thời gian một cách tăng dần. Chìa khóa là biết nó cần được chia nhỏ trước khi việc chia nhỏ trở nên quá tốn kém.

Nhưng trong thực tế, nhiều người trong chúng ta sẽ thấy các dịch vụ phát triển vượt xa điểm lành mạnh. Mặc dù biết rằng một tập hợp các dịch vụ nhỏ hơn sẽ dễ đối phó hơn con quái vật khổng lồ mà chúng ta hiện có, chúng ta vẫn tiếp tục phát triển con thú. Tại sao?

Một phần của vấn đề là biết bắt đầu từ đâu, và tôi hy vọng chương này đã giúp ích. Nhưng một thách thức khác là chi phí liên quan đến việc chia nhỏ các dịch vụ. Tìm một nơi để chạy dịch vụ, khởi động một `stack` dịch vụ mới, và vân vân, là những nhiệm vụ không hề tầm thường. Vậy làm thế nào để chúng ta giải quyết vấn đề này? Chà, nếu làm một việc gì đó là đúng nhưng lại khó, chúng ta nên cố gắng làm cho mọi việc trở nên dễ dàng hơn. Đầu tư vào các thư viện và các `framework` dịch vụ nhẹ có thể làm giảm chi phí liên quan đến việc tạo ra dịch vụ mới. Việc cho mọi người quyền truy cập vào việc tự cấp phát máy ảo hoặc thậm chí làm cho một **nền tảng như một dịch vụ (Platform as a Service - PaaS)** có sẵn sẽ giúp việc cấp phát hệ thống và kiểm thử chúng dễ dàng hơn. Trong phần còn lại của cuốn sách, chúng ta sẽ thảo luận về một số cách để giúp bạn giữ chi phí này ở mức thấp.

#### **Tóm tắt (Summary)**

Chúng ta phân tách hệ thống của mình bằng cách tìm kiếm các **đường nối (seams)** dọc theo đó các ranh giới dịch vụ có thể xuất hiện, và đây có thể là một cách tiếp cận tăng dần. Bằng cách trở nên giỏi trong việc tìm kiếm các đường nối này và làm việc để giảm chi phí chia nhỏ các dịch vụ ngay từ đầu, chúng ta có thể tiếp tục phát triển và tiến hóa hệ thống của mình để đáp ứng bất kỳ yêu cầu nào xuất hiện trên đường đi. Như bạn có thể thấy, một số công việc này có thể rất tỉ mỉ. Nhưng chính thực tế là nó có thể được thực hiện một cách tăng dần có nghĩa là không cần phải sợ hãi công việc này.

Vậy bây giờ chúng ta có thể chia nhỏ các dịch vụ của mình, nhưng chúng ta cũng đã giới thiệu một số vấn đề mới. Bây giờ chúng ta có nhiều bộ phận chuyển động hơn để đưa vào sản xuất! Vì vậy, tiếp theo chúng ta sẽ đi sâu vào thế giới của **triển khai (deployment)**.

*(Kết thúc Chương 5. Chương tiếp theo, Chương 6, sẽ tập trung vào "Triển khai".)*


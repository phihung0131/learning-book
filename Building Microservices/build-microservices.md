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

Tuyệt vời! Chúng ta sẽ bắt đầu Chương 6: "Triển khai (Deployment)". Chương này sẽ khám phá các kỹ thuật và công nghệ để triển khai microservices một cách hiệu quả.

---

### **6. Triển khai (Deployment)**

Triển khai một ứng dụng `monolithic` là một quy trình khá đơn giản. Microservices, với sự phụ thuộc lẫn nhau, lại là một ấm cá hoàn toàn khác. Nếu bạn không tiếp cận việc triển khai đúng cách, đó là một trong những lĩnh vực mà sự phức tạp có thể biến cuộc sống của bạn thành một cơn ác mộng. Trong chương này, chúng ta sẽ xem xét một số kỹ thuật và công nghệ có thể giúp chúng ta khi triển khai microservices vào các kiến trúc chi tiết.

Tuy nhiên, chúng ta sẽ bắt đầu bằng cách xem xét **tích hợp liên tục (continuous integration)** và **phân phối liên tục (continuous delivery)**. Những khái niệm liên quan nhưng khác biệt này sẽ giúp định hình các quyết định khác mà chúng ta sẽ đưa ra khi suy nghĩ về những gì cần xây dựng, cách xây dựng nó, và cách triển khai nó.

#### **Giới thiệu Sơ lược về Tích hợp Liên tục (A Brief Introduction to Continuous Integration)**

**Tích hợp liên tục (Continuous Integration - CI)** đã tồn tại được một số năm. Tuy nhiên, đáng để dành một chút thời gian để xem lại những điều cơ bản, đặc biệt là khi chúng ta nghĩ về sự ánh xạ giữa microservices, các bản dựng, và các kho lưu trữ kiểm soát phiên bản, có một số tùy chọn khác nhau cần xem xét.

Với CI, mục tiêu cốt lõi là giữ cho mọi người đồng bộ với nhau, điều mà chúng ta đạt được bằng cách đảm bảo rằng mã mới được `check-in` tích hợp đúng cách với mã hiện có. Để làm điều này, một máy chủ CI phát hiện rằng mã đã được `commit`, `check-out` nó, và thực hiện một số xác minh như đảm bảo mã biên dịch được và các bài kiểm thử vượt qua.

Là một phần của quá trình này, chúng ta thường tạo ra các **tạo tác (artifact(s))** được sử dụng để xác thực thêm, chẳng hạn như triển khai một dịch vụ đang chạy để chạy các bài kiểm thử chống lại nó. Lý tưởng nhất, chúng ta muốn xây dựng các tạo tác này một lần và chỉ một lần, và sử dụng chúng cho tất cả các lần triển khai của phiên bản mã đó. Điều này là để tránh làm cùng một việc lặp đi lặp lại, và để chúng ta có thể xác nhận rằng tạo tác chúng ta đã triển khai là cái chúng ta đã kiểm thử. Để cho phép các tạo tác này được tái sử dụng, chúng ta đặt chúng vào một loại kho lưu trữ nào đó, được cung cấp bởi chính công cụ CI hoặc trên một hệ thống riêng biệt.

Chúng ta sẽ xem xét các loại tạo tác nào chúng ta có thể sử dụng cho microservices ngay sau đây, và chúng ta sẽ xem xét sâu hơn về việc kiểm thử trong **Chương 7**.

CI có một số lợi ích. Chúng ta nhận được một mức độ phản hồi nhanh về chất lượng mã của mình. Nó cho phép chúng ta tự động hóa việc tạo ra các tạo tác nhị phân của mình. Tất cả mã cần thiết để xây dựng tạo tác đều được kiểm soát phiên bản, vì vậy chúng ta có thể tạo lại tạo tác nếu cần. Chúng ta cũng có được một mức độ truy xuất nguồn gốc từ một tạo tác đã triển khai trở lại mã nguồn, và tùy thuộc vào khả năng của chính công cụ CI, có thể xem các bài kiểm thử nào đã được chạy trên mã và tạo tác đó. Chính vì những lý do này mà CI đã rất thành công.

##### **Bạn có thực sự đang làm điều đó không? (Are You Really Doing It?)**

Tôi nghi ngờ rằng bạn có lẽ đang sử dụng tích hợp liên tục trong tổ chức của riêng mình. Nếu không, bạn nên bắt đầu. Đó là một thực hành quan trọng cho phép chúng ta thực hiện các thay đổi một cách nhanh chóng và dễ dàng, và nếu không có nó, hành trình vào microservices sẽ rất đau đớn. Điều đó nói rằng, tôi đã làm việc với nhiều đội, mặc dù nói rằng họ làm CI, nhưng thực ra không hề làm gì cả. Họ nhầm lẫn việc sử dụng một công cụ CI với việc áp dụng thực hành CI. Công cụ chỉ là thứ cho phép cách tiếp cận.

Tôi thực sự thích ba câu hỏi của Jez Humble mà ông hỏi mọi người để kiểm tra xem họ có thực sự hiểu CI là gì không:

*   **Bạn có `check-in` vào `mainline` mỗi ngày một lần không?**
    Bạn cần đảm bảo mã của mình tích hợp. Nếu bạn không kiểm tra mã của mình cùng với những thay đổi của mọi người khác thường xuyên, bạn sẽ làm cho việc tích hợp trong tương lai trở nên khó khăn hơn. Ngay cả khi bạn đang sử dụng các nhánh sống ngắn để quản lý các thay đổi, hãy tích hợp thường xuyên nhất có thể vào một nhánh `mainline` duy nhất.
*   **Bạn có một bộ kiểm thử để xác thực các thay đổi của mình không?**
    Nếu không có các bài kiểm thử, chúng ta chỉ biết rằng về mặt cú pháp, sự tích hợp của chúng ta đã hoạt động, nhưng chúng ta không biết liệu chúng ta có làm hỏng hành vi của hệ thống hay không. CI mà không có một số xác minh rằng mã của chúng ta hoạt động như mong đợi không phải là CI.
*   **Khi bản dựng bị hỏng, đó có phải là ưu tiên số 1 của đội để sửa nó không?**
    Một bản dựng màu xanh lá cây (`green build`) thành công có nghĩa là các thay đổi của chúng ta đã được tích hợp một cách an toàn. Một bản dựng màu đỏ (`red build`) có nghĩa là thay đổi cuối cùng có thể đã không tích hợp. Bạn cần phải dừng tất cả các `check-in` khác không liên quan đến việc sửa các bản dựng để nó hoạt động trở lại. Nếu bạn để nhiều thay đổi chồng chất lên nhau, thời gian cần thiết để sửa bản dựng sẽ tăng lên đáng kể. Tôi đã làm việc với các đội nơi bản dựng đã bị hỏng trong nhiều ngày, dẫn đến những nỗ lực đáng kể để cuối cùng có được một bản dựng thành công.

---
#### **Ánh xạ Tích hợp Liên tục vào Microservices (Mapping Continuous Integration to Microservices)**

Khi suy nghĩ về microservices và tích hợp liên tục, chúng ta cần suy nghĩ về cách các bản dựng CI của chúng ta ánh xạ đến các microservice riêng lẻ. Như tôi đã nói nhiều lần, chúng ta muốn đảm bảo rằng chúng ta có thể thực hiện một thay đổi cho một dịch vụ duy nhất và triển khai nó một cách độc lập với phần còn lại. Với ý nghĩ đó, chúng ta nên ánh xạ các microservice riêng lẻ đến các bản dựng CI và mã nguồn như thế nào?

##### **Một kho lưu trữ, một bản dựng cho tất cả**

Nếu chúng ta bắt đầu với tùy chọn đơn giản nhất, chúng ta có thể gộp mọi thứ lại với nhau. Chúng ta có một kho lưu trữ khổng lồ duy nhất lưu trữ tất cả mã của chúng ta, và có một bản dựng duy nhất, như chúng ta thấy trong Hình 6-1. Bất kỳ `check-in` nào vào kho lưu trữ mã nguồn này sẽ kích hoạt bản dựng của chúng ta, nơi chúng ta sẽ chạy tất cả các bước xác minh liên quan đến tất cả các microservice của mình, và tạo ra nhiều tạo tác, tất cả đều được gắn với cùng một bản dựng.

![alt text](<images/Screenshot from 2025-09-29 13-04-37.png>)

**Hình 6-1.** *Sử dụng một kho lưu trữ mã nguồn duy nhất và bản dựng CI cho tất cả các microservice*

Điều này có vẻ đơn giản hơn nhiều trên bề mặt so với các cách tiếp cận khác: ít kho lưu trữ hơn để lo lắng, và một bản dựng đơn giản hơn về mặt khái niệm. Từ quan điểm của một nhà phát triển, mọi thứ cũng khá đơn giản. Tôi chỉ cần `check in` mã. Nếu tôi phải làm việc trên nhiều dịch vụ cùng một lúc, tôi chỉ phải lo lắng về một `commit` duy nhất.

Mô hình này có thể hoạt động hoàn hảo nếu bạn chấp nhận ý tưởng về các bản phát hành đồng bộ (`lock-step releases`), nơi bạn không ngại triển khai nhiều dịch vụ cùng một lúc. Nói chung, đây là một mẫu hình cần tránh, nhưng rất sớm trong một dự án, đặc biệt nếu chỉ có một đội đang làm việc trên mọi thứ, điều này có thể có ý nghĩa trong một khoảng thời gian ngắn.

Tuy nhiên, có một số nhược điểm đáng kể. Nếu tôi thực hiện một thay đổi một dòng cho một dịch vụ duy nhất—ví dụ, thay đổi hành vi trong dịch vụ người dùng trong Hình 6-1—tất cả các dịch vụ khác đều được xác minh và xây dựng. Điều này có thể mất nhiều thời gian hơn cần thiết—tôi đang chờ đợi những thứ có lẽ không cần phải được kiểm thử. Điều này ảnh hưởng đến **thời gian chu kỳ (cycle time)** của chúng ta, tốc độ mà chúng ta có thể di chuyển một thay đổi duy nhất từ phát triển đến sản xuất. Tuy nhiên, điều đáng lo ngại hơn là biết tạo tác nào nên hoặc không nên được triển khai. Bây giờ tôi có cần triển khai tất cả các dịch vụ đã được xây dựng để đẩy thay đổi nhỏ của mình vào sản xuất không? Có thể khó nói; việc cố gắng đoán dịch vụ nào thực sự đã thay đổi chỉ bằng cách đọc các thông báo `commit` là rất khó. Các tổ chức sử dụng cách tiếp cận này thường quay trở lại việc chỉ triển khai mọi thứ cùng nhau, điều mà chúng ta thực sự muốn tránh.

Hơn nữa, nếu thay đổi một dòng của tôi vào dịch vụ người dùng làm hỏng bản dựng, không có thay đổi nào khác có thể được thực hiện cho các dịch vụ khác cho đến khi sự cố đó được khắc phục. Và hãy nghĩ về một kịch bản nơi bạn có nhiều đội cùng chia sẻ bản dựng khổng lồ này. Ai chịu trách nhiệm?

##### **Một kho lưu trữ, nhiều bản dựng độc lập**

Một biến thể của cách tiếp cận này là có một cây nguồn duy nhất với tất cả mã trong đó, với nhiều bản dựng CI ánh xạ đến các phần của cây nguồn đó, như chúng ta thấy trong Hình 6-2. Với cấu trúc được xác định rõ ràng, bạn có thể dễ dàng ánh xạ các bản dựng đến các phần nhất định của cây nguồn. Nói chung, tôi không phải là một người hâm mộ cách tiếp cận này, vì mô hình này có thể là một phước lành lẫn lộn. Một mặt, quy trình `check-in/check-out` của tôi có thể đơn giản hơn vì tôi chỉ có một kho lưu trữ để lo lắng. Mặt khác, nó trở nên rất dễ dàng để có thói quen `check-in` mã nguồn cho nhiều dịch vụ cùng một lúc, điều này có thể dễ dàng dẫn đến việc thực hiện các thay đổi ghép nối các dịch vụ lại với nhau. Tuy nhiên, tôi sẽ rất thích cách tiếp cận này hơn là có một bản dựng duy nhất cho nhiều dịch vụ.

![alt text](<images/Screenshot from 2025-09-29 13-04-50.png>)

**Hình 6-2.** *Một kho lưu trữ nguồn duy nhất với các thư mục con được ánh xạ đến các bản dựng độc lập*

##### **Một bản dựng cho mỗi dịch vụ**

Vậy có một giải pháp thay thế nào khác không? Cách tiếp cận mà tôi ưa thích là có **một bản dựng CI cho mỗi microservice**, để cho phép chúng ta nhanh chóng thực hiện và xác thực một thay đổi trước khi triển khai vào sản xuất, như được hiển thị trong Hình 6-3. Ở đây mỗi microservice có kho lưu trữ mã nguồn riêng, được ánh xạ đến bản dựng CI riêng của nó. Khi thực hiện một thay đổi, tôi chỉ chạy bản dựng và các bài kiểm thử tôi cần. Tôi nhận được một tạo tác duy nhất để triển khai. Sự phù hợp với quyền sở hữu của đội cũng rõ ràng hơn. Nếu bạn sở hữu dịch vụ, bạn sở hữu kho lưu trữ và bản dựng. Việc thực hiện các thay đổi trên các kho lưu trữ có thể khó khăn hơn trong thế giới này, nhưng tôi sẽ cho rằng điều này dễ giải quyết hơn (ví dụ, bằng cách sử dụng các tập lệnh dòng lệnh) so với nhược điểm của việc kiểm soát nguồn và quy trình xây dựng `monolithic`.

![alt text](<images/Screenshot from 2025-09-29 13-04-56.png>)

**Hình 6-3.** *Sử dụng một kho lưu trữ mã nguồn và bản dựng CI cho mỗi microservice*

Các bài kiểm thử cho một microservice nhất định nên nằm trong kiểm soát nguồn cùng với mã nguồn của microservice đó, để đảm bảo chúng ta luôn biết các bài kiểm thử nào nên được chạy đối với một dịch vụ nhất định.

Vì vậy, mỗi microservice sẽ nằm trong kho lưu trữ mã nguồn riêng của nó, và quy trình xây dựng CI riêng của nó. Chúng ta cũng sẽ sử dụng quy trình xây dựng CI để tạo ra các tạo tác có thể triển khai của mình một cách hoàn toàn tự động. Bây giờ hãy nhìn xa hơn CI để xem phân phối liên tục phù hợp như thế nào.

*(Phần tiếp theo sẽ là "Build Pipelines and Continuous Delivery".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với "Build Pipelines and Continuous Delivery", một khái niệm mở rộng từ CI để quản lý toàn bộ quá trình phát hành phần mềm.

---

#### **Đường ống Xây dựng và Phân phối Liên tục (Build Pipelines and Continuous Delivery)**

Rất sớm trong quá trình sử dụng tích hợp liên tục, chúng tôi đã nhận ra giá trị của việc đôi khi có nhiều giai đoạn bên trong một bản dựng. Các bài kiểm thử là một trường hợp rất phổ biến mà điều này phát huy tác dụng. Tôi có thể có rất nhiều bài kiểm thử nhanh, phạm vi nhỏ, và một số lượng nhỏ các bài kiểm thử phạm vi lớn, chậm. Nếu chúng ta chạy tất cả các bài kiểm thử cùng nhau, chúng ta có thể không nhận được phản hồi nhanh khi các bài kiểm thử nhanh của chúng ta thất bại nếu chúng ta đang chờ đợi các bài kiểm thử chậm, phạm vi lớn của mình cuối cùng hoàn thành. Và nếu các bài kiểm thử nhanh thất bại, có lẽ không có nhiều ý nghĩa khi chạy các bài kiểm thử chậm hơn! Một giải pháp cho vấn đề này là có các giai đoạn khác nhau trong bản dựng của chúng ta, tạo ra cái được gọi là **đường ống xây dựng (build pipeline)**. Một giai đoạn cho các bài kiểm thử nhanh hơn, một giai đoạn cho các bài kiểm thử chậm hơn.

Khái niệm đường ống xây dựng này cung cấp cho chúng ta một cách hay để theo dõi tiến trình của phần mềm của chúng ta khi nó vượt qua từng giai đoạn, giúp chúng ta có cái nhìn sâu sắc về chất lượng phần mềm của mình. Chúng ta xây dựng tạo tác của mình, và tạo tác đó được sử dụng trong suốt đường ống. Khi tạo tác của chúng ta di chuyển qua các giai đoạn này, chúng ta cảm thấy ngày càng tự tin hơn rằng phần mềm sẽ hoạt động trong sản xuất.

**Phân phối liên tục (Continuous Delivery - CD)** xây dựng trên khái niệm này, và còn hơn thế nữa. Như được nêu trong cuốn sách cùng tên của Jez Humble và Dave Farley, phân phối liên tục là cách tiếp cận theo đó chúng ta nhận được phản hồi liên tục về sự sẵn sàng sản xuất của mỗi lần `check-in`, và hơn nữa coi mỗi lần `check-in` như một ứng cử viên phát hành.

Để hoàn toàn chấp nhận khái niệm này, chúng ta cần mô hình hóa tất cả các quy trình liên quan đến việc đưa phần mềm của chúng ta từ `check-in` đến sản xuất, và biết bất kỳ phiên bản nào của phần mềm đang ở đâu về mặt được xóa để phát hành. Trong CD, chúng ta làm điều này bằng cách mở rộng ý tưởng về đường ống xây dựng đa giai đoạn để mô hình hóa mọi giai đoạn mà phần mềm của chúng ta phải trải qua, cả thủ công và tự động. Trong Hình 6-4, chúng ta thấy một đường ống mẫu có thể quen thuộc.

![alt text](<images/Screenshot from 2025-09-29 13-05-02.png>)

**Hình 6-4.** *Một quy trình phát hành tiêu chuẩn được mô hình hóa như một đường ống xây dựng*

Ở đây, chúng ta thực sự muốn một công cụ chấp nhận CD như một khái niệm hạng nhất. Tôi đã thấy nhiều người cố gắng hack và mở rộng các công cụ CI để bắt chúng làm CD, thường dẫn đến các hệ thống phức tạp không dễ sử dụng bằng các công cụ xây dựng CD ngay từ đầu. Các công cụ hỗ trợ đầy đủ CD cho phép bạn định nghĩa và trực quan hóa các đường ống này, mô hình hóa toàn bộ con đường đến sản xuất cho phần mềm của bạn. Khi một phiên bản mã của chúng ta di chuyển qua đường ống, nếu nó vượt qua một trong những bước xác minh tự động này, nó sẽ chuyển sang giai đoạn tiếp theo. Các giai đoạn khác có thể là thủ công. Ví dụ, nếu chúng ta có một quy trình kiểm thử chấp nhận người dùng (`user acceptance testing - UAT`) thủ công, tôi nên có thể sử dụng một công cụ CD để mô hình hóa nó. Tôi có thể thấy bản dựng có sẵn tiếp theo sẵn sàng để được triển khai vào môi trường UAT của chúng ta, triển khai nó, và nếu nó vượt qua các kiểm tra thủ công của chúng ta, đánh dấu giai đoạn đó là thành công để nó có thể chuyển sang giai đoạn tiếp theo.

Bằng cách mô hình hóa toàn bộ con đường đến sản xuất cho phần mềm của mình, chúng ta cải thiện đáng kể khả năng hiển thị về chất lượng phần mềm của mình, và cũng có thể giảm đáng kể thời gian giữa các bản phát hành, vì chúng ta có một nơi để quan sát quy trình xây dựng và phát hành của mình, và một điểm tập trung rõ ràng để giới thiệu các cải tiến.

Trong một thế giới microservices, nơi chúng ta muốn đảm bảo chúng ta có thể phát hành các dịch vụ của mình một cách độc lập với nhau, theo đó, cũng như với CI, chúng ta sẽ muốn **một đường ống cho mỗi dịch vụ**. Trong các đường ống của chúng ta, đó là một **tạo tác (artifact)** mà chúng ta muốn tạo ra và di chuyển qua con đường đến sản xuất của chúng ta. Như mọi khi, hóa ra các tạo tác của chúng ta có thể có nhiều kích cỡ và hình dạng. Chúng ta sẽ xem xét một số tùy chọn phổ biến nhất có sẵn cho chúng ta trong giây lát.

#### **Và những Ngoại lệ Không thể tránh khỏi (And the Inevitable Exceptions)**

Như với tất cả các quy tắc tốt, có những ngoại lệ chúng ta cần xem xét. Cách tiếp cận "một microservice cho mỗi bản dựng" hoàn toàn là điều bạn nên hướng tới, nhưng có những lúc một cái gì đó khác lại có ý nghĩa hơn không? Khi một đội bắt đầu một dự án mới, đặc biệt là một dự án `greenfield` nơi họ đang làm việc với một tờ giấy trắng, rất có khả năng sẽ có một lượng lớn sự biến động về việc tìm ra các ranh giới dịch vụ nằm ở đâu. Đây là một lý do tốt, trên thực tế, để giữ các dịch vụ ban đầu của bạn ở phía lớn hơn cho đến khi sự hiểu biết của bạn về miền nghiệp vụ ổn định.

Trong thời gian biến động này, các thay đổi trên các ranh giới dịch vụ có nhiều khả năng xảy ra hơn, và những gì có trong hoặc không có trong một dịch vụ nhất định có khả năng thay đổi thường xuyên. Trong giai đoạn này, việc có tất cả các dịch vụ trong một bản dựng duy nhất để giảm chi phí của các thay đổi xuyên dịch vụ có thể có ý nghĩa.

Tuy nhiên, điều đó có nghĩa là, trong trường hợp này, bạn cần phải chấp nhận việc phát hành tất cả các dịch vụ như một gói. Nó cũng hoàn toàn cần phải là một bước chuyển tiếp. Khi các API dịch vụ ổn định, hãy bắt đầu chuyển chúng ra các bản dựng riêng của chúng. Nếu sau vài tuần (hoặc một số tháng rất nhỏ) bạn không thể có được sự ổn định trong các ranh giới dịch vụ để tách chúng ra một cách đúng đắn, hãy hợp nhất chúng trở lại thành một dịch vụ `monolithic` hơn (mặc dù vẫn giữ sự tách biệt `module` trong ranh giới) và cho mình thời gian để làm quen với miền nghiệp vụ. Điều này phản ánh kinh nghiệm của chính đội SnapCI của chúng tôi, như chúng ta đã thảo luận trong **Chương 3**.

---
#### **Tạo tác Cụ thể cho Nền tảng (Platform-Specific Artifacts)**

Hầu hết các `technology stack` đều có một loại tạo tác hạng nhất nào đó, cùng với các công cụ để hỗ trợ việc tạo và cài đặt chúng. Ruby có `gems`, Java có các tệp `JAR` và `WAR`, và Python có `eggs`. Các nhà phát triển có kinh nghiệm trong một trong những `stack` này sẽ rất quen thuộc với việc làm việc với (và hy vọng là tạo ra) những tạo tác này.

Tuy nhiên, từ quan điểm của một microservice, tùy thuộc vào `technology stack` của bạn, tạo tác này có thể không đủ. Mặc dù một tệp `JAR` của Java có thể được làm để có thể thực thi và chạy một tiến trình `HTTP` nhúng, đối với những thứ như các ứng dụng Ruby và Python, bạn sẽ mong đợi sử dụng một trình quản lý tiến trình chạy bên trong Apache hoặc Nginx. Vì vậy, chúng ta có thể cần một cách nào đó để cài đặt và cấu hình phần mềm khác mà chúng ta cần để triển khai và khởi chạy các tạo tác của mình. Đây là nơi các công cụ quản lý cấu hình tự động như Puppet và Chef có thể giúp ích.

Một nhược điểm khác ở đây là những tạo tác này cụ thể cho một `technology stack` nhất định, điều này có thể làm cho việc triển khai trở nên khó khăn hơn khi chúng ta có sự kết hợp của các công nghệ đang hoạt động. Hãy nghĩ về nó từ quan điểm của một người đang cố gắng triển khai nhiều dịch vụ cùng nhau. Họ có thể là một nhà phát triển hoặc một người kiểm thử muốn kiểm tra một số chức năng, hoặc có thể là một người quản lý một đợt triển khai sản xuất. Bây giờ hãy tưởng tượng rằng những dịch vụ đó sử dụng ba cơ chế triển khai hoàn toàn khác nhau. Có lẽ chúng ta có một `Ruby Gem`, một tệp `JAR`, và một `package nodeJS NPM`. Liệu họ có cảm ơn bạn không?

Tự động hóa có thể đi một chặng đường dài trong việc che giấu sự khác biệt trong các cơ chế triển khai của các tạo tác cơ bản. Chef, Puppet, và Ansible đều hỗ trợ nhiều tạo tác xây dựng cụ thể cho công nghệ phổ biến khác nhau. Nhưng có những loại tạo tác khác có thể còn dễ làm việc hơn.

*(Phần tiếp theo sẽ thảo luận về "Operating System Artifacts" và "Custom Images".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục khám phá các loại tạo tác (`artifacts`) khác nhau, bắt đầu với các tạo tác ở cấp độ hệ điều hành.

---

#### **Tạo tác Hệ điều hành (Operating System Artifacts)**

Một cách để tránh các vấn đề liên quan đến các tạo tác cụ thể cho công nghệ là tạo ra các tạo tác gốc (`native`) cho hệ điều hành cơ bản. Ví dụ, đối với một hệ thống dựa trên RedHat– hoặc CentOS, tôi có thể xây dựng các gói `RPM`; đối với Ubuntu, tôi có thể xây dựng một gói `deb`; hoặc đối với Windows, một tệp `MSI`.

Lợi thế của việc sử dụng các tạo tác cụ thể cho HĐH là từ quan điểm triển khai, chúng ta không quan tâm công nghệ cơ bản là gì. Chúng ta chỉ cần sử dụng các công cụ gốc của HĐH để cài đặt gói. Các công cụ HĐH cũng có thể giúp chúng ta gỡ cài đặt và lấy thông tin về các gói, và thậm chí có thể cung cấp các kho lưu trữ gói mà các công cụ CI của chúng ta có thể đẩy lên. Phần lớn công việc được thực hiện bởi trình quản lý gói HĐH cũng có thể bù đắp cho công việc mà bạn có thể phải làm trong một công cụ như Puppet hoặc Chef. Ví dụ, trên tất cả các nền tảng Linux tôi đã sử dụng, bạn có thể định nghĩa các phụ thuộc từ các gói của mình đến các gói khác mà bạn dựa vào, và các công cụ HĐH sẽ tự động cài đặt chúng cho bạn.

Nhược điểm có thể là khó khăn trong việc tạo ra các gói ngay từ đầu. Đối với Linux, công cụ quản lý gói **FPM** cung cấp một sự trừu tượng hóa tốt hơn để tạo các gói HĐH Linux, và việc chuyển đổi từ một triển khai dựa trên `tarball` sang một triển khai dựa trên HĐH có thể khá đơn giản. Không gian Windows có phần phức tạp hơn. Hệ thống đóng gói gốc dưới dạng các trình cài đặt `MSI` và tương tự để lại rất nhiều điều đáng mong đợi khi so sánh với các khả năng trong không gian Linux. Hệ thống gói `NuGet` đã bắt đầu giúp giải quyết vấn đề này, ít nhất là về mặt giúp quản lý các thư viện phát triển. Gần đây hơn, **Chocolatey NuGet** đã mở rộng những ý tưởng này, cung cấp một trình quản lý gói cho Windows được thiết kế để triển khai các công cụ và dịch vụ, giống như các trình quản lý gói trong không gian Linux hơn. Đây chắc chắn là một bước đi đúng hướng, mặc dù thực tế là phong cách thành ngữ (`idiomatic style`) trong Windows vẫn là triển khai một cái gì đó trong IIS có nghĩa là cách tiếp cận này có thể không hấp dẫn đối với một số đội Windows.

Tất nhiên, một nhược điểm khác có thể là nếu bạn đang triển khai trên nhiều hệ điều hành khác nhau. Chi phí quản lý các tạo tác cho các HĐH khác nhau có thể khá cao. Nếu bạn đang tạo phần mềm để người khác cài đặt, bạn có thể không có lựa chọn nào khác. Tuy nhiên, nếu bạn đang cài đặt phần mềm trên các máy bạn kiểm soát, tôi sẽ đề nghị bạn xem xét việc thống nhất hoặc ít nhất là giảm số lượng các hệ điều hành khác nhau bạn sử dụng. Nó có thể giảm đáng kể sự thay đổi trong hành vi từ máy này sang máy khác, và đơn giản hóa việc triển khai và các tác vụ bảo trì.

Nói chung, những đội mà tôi đã thấy đã chuyển sang quản lý gói dựa trên HĐH đã đơn giản hóa cách tiếp cận triển khai của họ, và có xu hướng tránh bẫy của các tập lệnh triển khai lớn, phức tạp. Đặc biệt nếu bạn đang sử dụng Linux, đây có thể là một cách tốt để đơn giản hóa việc triển khai các microservice sử dụng các `technology stack` khác nhau.

#### **Hình ảnh Tùy chỉnh (Custom Images)**

Một trong những thách thức với các hệ thống quản lý cấu hình tự động như Puppet, Chef, và Ansible có thể là thời gian cần thiết để chạy các tập lệnh trên một máy. Hãy lấy một ví dụ đơn giản về một máy chủ được cấp phát và cấu hình để cho phép triển khai một ứng dụng Java. Giả sử tôi đang sử dụng AWS để cấp phát máy chủ, sử dụng hình ảnh Ubuntu tiêu chuẩn. Điều đầu tiên tôi cần làm là cài đặt Oracle JVM để chạy ứng dụng Java của mình. Tôi đã thấy quá trình đơn giản này mất khoảng năm phút, với một vài phút được dành cho việc cấp phát máy, và một vài phút nữa để cài đặt JVM. Sau đó, chúng ta có thể nghĩ đến việc thực sự đặt phần mềm của mình lên đó.

Đây thực sự là một ví dụ khá tầm thường. Chúng ta thường sẽ muốn cài đặt các phần mềm phổ biến khác. Ví dụ, chúng ta có thể muốn sử dụng `collectd` để thu thập các chỉ số HĐH, sử dụng `logstash` để tổng hợp nhật ký, và có lẽ cài đặt các phần phù hợp của `nagios` để giám sát (chúng ta sẽ nói nhiều hơn về phần mềm này trong **Chương 8**). Theo thời gian, nhiều thứ khác có thể được thêm vào, dẫn đến thời gian ngày càng dài cần thiết để cấp phát các phụ thuộc này.

Puppet, Chef, Ansible, và những thứ tương tự có thể thông minh và sẽ tránh cài đặt phần mềm đã có sẵn. Thật không may, điều này không có nghĩa là việc chạy các tập lệnh trên các máy hiện có sẽ luôn nhanh chóng, vì việc chạy tất cả các kiểm tra đều mất thời gian. Chúng ta cũng muốn tránh giữ máy của mình quá lâu, vì chúng ta không muốn cho phép quá nhiều **trôi dạt cấu hình (configuration drift)** (mà chúng ta sẽ khám phá sâu hơn ngay sau đây). Và nếu chúng ta đang sử dụng một nền tảng điện toán theo yêu cầu, chúng ta có thể liên tục tắt và khởi động các phiên bản mới hàng ngày (nếu không muốn nói là thường xuyên hơn), vì vậy bản chất khai báo của các công cụ quản lý cấu hình này có thể có công dụng hạn chế.

Theo thời gian, việc xem các công cụ giống nhau được cài đặt lặp đi lặp lại có thể trở thành một sự phiền toái thực sự. Nếu bạn đang cố gắng làm điều này nhiều lần mỗi ngày—có lẽ như một phần của việc phát triển hoặc CI—điều này trở thành một vấn đề thực sự về mặt cung cấp phản hồi nhanh. Nó cũng có thể dẫn đến thời gian ngừng hoạt động tăng lên khi triển khai trong sản xuất nếu hệ thống của bạn không cho phép triển khai không có thời gian ngừng hoạt động (`zero-downtime deployment`), vì bạn đang chờ đợi để cài đặt tất cả các điều kiện tiên quyết trên máy của mình ngay cả trước khi bạn cài đặt phần mềm của mình. Các mô hình như triển khai **blue/green** (mà chúng ta sẽ thảo luận trong **Chương 7**) có thể giúp giảm thiểu điều này, vì chúng cho phép chúng ta triển khai một phiên bản mới của dịch vụ mà không cần phải gỡ bỏ phiên bản cũ.

Một cách tiếp cận để giảm thời gian khởi động này là tạo ra một **hình ảnh máy ảo (virtual machine image)** tích hợp sẵn một số phụ thuộc chung mà chúng ta sử dụng, như được hiển thị trong Hình 6-5. Tất cả các nền tảng ảo hóa mà tôi đã sử dụng đều cho phép bạn xây dựng hình ảnh của riêng mình, và các công cụ để làm điều đó tiên tiến hơn nhiều so với chỉ vài năm trước. Điều này thay đổi mọi thứ một chút. Bây giờ chúng ta có thể tích hợp các công cụ chung vào hình ảnh của riêng mình. Khi chúng ta muốn triển khai phần mềm của mình, chúng ta khởi động một phiên bản của hình ảnh tùy chỉnh này, và tất cả những gì chúng ta phải làm là cài đặt phiên bản mới nhất của dịch vụ của mình.

![alt text](<images/Screenshot from 2025-09-29 13-05-13.png>)

**Hình 6-5.** *Tạo một hình ảnh VM tùy chỉnh*

Tất nhiên, bởi vì bạn chỉ xây dựng hình ảnh một lần, khi bạn sau đó khởi chạy các bản sao của hình ảnh này, bạn không cần phải dành thời gian cài đặt các phụ thuộc của mình, vì chúng đã có sẵn. Điều này có thể dẫn đến việc tiết kiệm thời gian đáng kể. Nếu các phụ thuộc cốt lõi của bạn không thay đổi, các phiên bản mới của dịch vụ của bạn có thể tiếp tục sử dụng cùng một hình ảnh cơ sở.

Tuy nhiên, có một số nhược điểm với cách tiếp cận này. Việc xây dựng hình ảnh có thể mất một thời gian dài. Điều này có nghĩa là đối với các nhà phát triển, bạn có thể muốn hỗ trợ các cách triển khai dịch vụ khác để đảm bảo họ không phải đợi nửa giờ chỉ để tạo ra một triển khai nhị phân. Thứ hai, một số hình ảnh kết quả có thể lớn. Đây có thể là một vấn đề thực sự nếu bạn đang tạo hình ảnh VMWare của riêng mình, ví dụ, vì việc di chuyển một hình ảnh 20GB qua mạng không phải lúc nào cũng là một hoạt động đơn giản. Chúng ta sẽ xem xét công nghệ `container` ngay sau đây, và cụ thể là Docker, có thể tránh được một số nhược điểm này.

Trong lịch sử, một trong những thách thức là chuỗi công cụ cần thiết để xây dựng một hình ảnh như vậy thay đổi từ nền tảng này sang nền tảng khác. Việc xây dựng một hình ảnh VMWare khác với việc xây dựng một `AMI` của AWS, một hình ảnh Vagrant, hoặc một hình ảnh Rackspace. Đây có thể không phải là vấn đề nếu bạn có cùng một nền tảng ở mọi nơi, nhưng không phải tất cả các tổ chức đều may mắn như vậy. Và ngay cả khi họ có, các công cụ trong lĩnh vực này thường khó làm việc, và chúng không hoạt động tốt với các công cụ khác bạn có thể đang sử dụng để cấu hình máy.

**Packer** là một công cụ được thiết kế để làm cho việc tạo hình ảnh trở nên dễ dàng hơn nhiều. Sử dụng các tập lệnh cấu hình bạn chọn (Chef, Ansible, Puppet, và nhiều hơn nữa được hỗ trợ), nó cho phép chúng ta tạo hình ảnh cho các nền tảng khác nhau từ cùng một cấu hình. Tại thời điểm viết bài, nó có hỗ trợ cho VMWare, AWS, Rackspace Cloud, Digital Ocean, và Vagrant, và tôi đã thấy các đội sử dụng nó thành công để xây dựng các hình ảnh Linux và Windows. Điều này có nghĩa là bạn có thể tạo một hình ảnh để triển khai trên môi trường AWS sản xuất của mình và một hình ảnh Vagrant phù hợp để phát triển và kiểm thử cục bộ, tất cả từ cùng một cấu hình.

##### **Hình ảnh như là Tạo tác (Images as Artifacts)**

Vậy là chúng ta có thể tạo ra các hình ảnh máy ảo tích hợp sẵn các phụ thuộc để tăng tốc độ phản hồi, nhưng tại sao lại dừng lại ở đó? Chúng ta có thể đi xa hơn, tích hợp dịch vụ của mình vào chính hình ảnh đó, và áp dụng mô hình tạo tác dịch vụ của chúng ta là một hình ảnh. Bây giờ, khi chúng ta khởi chạy hình ảnh của mình, dịch vụ của chúng ta đã sẵn sàng hoạt động. Thời gian khởi động thực sự nhanh chóng này là lý do mà Netflix đã áp dụng mô hình tích hợp các dịch vụ của riêng mình dưới dạng `AMI` của AWS.

Cũng như với các gói cụ thể cho HĐH, các hình ảnh VM này trở thành một cách hay để trừu tượng hóa sự khác biệt trong các `technology stack` được sử dụng để tạo ra các dịch vụ. Chúng ta có quan tâm liệu dịch vụ đang chạy trên hình ảnh được viết bằng Ruby hay Java, và sử dụng một `gem` hay một tệp `JAR` không? Tất cả những gì chúng ta quan tâm là nó hoạt động. Sau đó, chúng ta có thể tập trung nỗ lực của mình vào việc tự động hóa việc tạo và triển khai các hình ảnh này. Đây cũng trở thành một cách thực sự gọn gàng để triển khai một khái niệm triển khai khác, **máy chủ bất biến (immutable server)**.

##### **Máy chủ Bất biến (Immutable Servers)**

Bằng cách lưu trữ tất cả cấu hình của chúng ta trong kiểm soát nguồn, chúng ta đang cố gắng đảm bảo rằng chúng ta có thể tự động tái tạo các dịch vụ và hy vọng là toàn bộ môi trường theo ý muốn. Nhưng một khi chúng ta chạy quy trình triển khai của mình, điều gì sẽ xảy ra nếu ai đó đến, đăng nhập vào hộp, và thay đổi mọi thứ một cách độc lập với những gì có trong kiểm soát nguồn? Vấn đề này thường được gọi là **trôi dạt cấu hình (configuration drift)**—mã trong kiểm soát nguồn không còn phản ánh cấu hình của máy chủ đang chạy nữa.

Để tránh điều này, chúng ta có thể đảm bảo rằng không có thay đổi nào được thực hiện đối với một máy chủ đang chạy. Thay vào đó, bất kỳ thay đổi nào, dù nhỏ đến đâu, cũng phải đi qua một đường ống xây dựng để tạo ra một máy mới. Bạn có thể triển khai mẫu hình này mà không cần sử dụng các triển khai dựa trên hình ảnh, nhưng nó cũng là một sự mở rộng hợp lý của việc sử dụng hình ảnh như là các tạo tác. Ví dụ, trong quá trình tạo hình ảnh của mình, chúng ta thực sự có thể vô hiệu hóa `SSH`, đảm bảo rằng không ai có thể đăng nhập vào hộp để thực hiện một thay đổi!

Tất nhiên, những lưu ý tương tự mà chúng ta đã thảo luận trước đó về thời gian chu kỳ vẫn áp dụng. Và chúng ta cũng cần đảm bảo rằng bất kỳ dữ liệu nào chúng ta quan tâm được lưu trữ trên hộp đều được lưu trữ ở nơi khác. Ngoài những sự phức tạp này, tôi đã thấy việc áp dụng mẫu hình này dẫn đến các triển khai đơn giản hơn nhiều, và các môi trường dễ lý giải hơn. Và như tôi đã nói, bất cứ điều gì chúng ta có thể làm để đơn giản hóa mọi thứ nên được theo đuổi!

*(Phần tiếp theo sẽ thảo luận về "Environments", "Service Configuration", và các mô hình ánh xạ dịch vụ-đến-máy chủ.)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với các phần còn lại của Chương 6, bao gồm các chủ đề quan trọng về môi trường, cấu hình và các mô hình triển khai khác nhau.

---

#### **Môi trường (Environments)**

Khi phần mềm của chúng ta di chuyển qua các giai đoạn của đường ống CD, nó cũng sẽ được triển khai vào các loại môi trường khác nhau. Nếu chúng ta nghĩ về đường ống xây dựng ví dụ trong Hình 6-4, có lẽ chúng ta phải xem xét ít nhất bốn môi trường riêng biệt: một môi trường nơi chúng ta chạy các bài kiểm thử chậm, một môi trường khác cho UAT, một môi trường khác cho hiệu suất, và một môi trường cuối cùng cho sản xuất. Microservice của chúng ta nên giống nhau trong suốt quá trình, nhưng môi trường sẽ khác nhau. Ít nhất, chúng sẽ là các tập hợp cấu hình và máy chủ riêng biệt, khác biệt. Nhưng thường thì chúng có thể khác nhau nhiều hơn thế. Ví dụ, môi trường sản xuất cho dịch vụ của chúng ta có thể bao gồm nhiều máy chủ được cân bằng tải trải rộng trên hai trung tâm dữ liệu, trong khi môi trường kiểm thử của chúng ta có thể chỉ có mọi thứ chạy trên một máy chủ duy nhất. Những sự khác biệt này trong các môi trường có thể gây ra một vài vấn đề.

Cá nhân tôi đã bị cắn bởi điều này nhiều năm trước. Chúng tôi đang triển khai một dịch vụ web Java vào một `container` ứng dụng WebLogic được phân cụm trong sản xuất. Cụm WebLogic này sao chép trạng thái phiên giữa nhiều nút, mang lại cho chúng tôi một mức độ phục hồi nào đó nếu một nút duy nhất bị lỗi. Tuy nhiên, các giấy phép WebLogic rất đắt, cũng như các máy mà phần mềm của chúng tôi được triển khai trên đó. Điều này có nghĩa là trong môi trường kiểm thử của chúng tôi, phần mềm của chúng tôi được triển khai trên một máy duy nhất, trong một cấu hình không phân cụm.

Điều này đã làm chúng tôi tổn thương nặng nề trong một lần phát hành. Để WebLogic có thể sao chép trạng thái phiên giữa các nút, dữ liệu phiên cần phải có khả năng tuần tự hóa đúng cách. Thật không may, một trong các `commit` của chúng tôi đã làm hỏng điều này, vì vậy khi chúng tôi triển khai vào sản xuất, việc sao chép phiên của chúng tôi đã thất bại. Cuối cùng, chúng tôi đã giải quyết vấn đề này bằng cách thúc đẩy mạnh mẽ để sao chép một thiết lập phân cụm trong môi trường kiểm thử của chúng tôi.

Dịch vụ chúng ta muốn triển khai là giống nhau trong tất cả các môi trường khác nhau này, nhưng mỗi môi trường lại phục vụ một mục đích khác nhau. Trên máy tính xách tay của nhà phát triển của tôi, tôi muốn nhanh chóng triển khai dịch vụ, có thể là chống lại các cộng tác viên được `stub`, để chạy các bài kiểm thử hoặc thực hiện một số xác minh thủ công về hành vi, trong khi khi tôi triển khai vào một môi trường sản xuất, tôi có thể muốn triển khai nhiều bản sao của dịch vụ của mình theo một kiểu được cân bằng tải, có thể được chia ra trên một hoặc nhiều trung tâm dữ liệu vì lý do độ bền.

Khi bạn di chuyển từ máy tính xách tay của mình đến máy chủ xây dựng đến môi trường UAT và đến sản xuất, bạn sẽ muốn đảm bảo rằng các môi trường của bạn ngày càng giống sản xuất hơn để phát hiện bất kỳ vấn đề nào liên quan đến những khác biệt về môi trường này sớm hơn. Đây sẽ là một sự cân bằng liên tục. Đôi khi thời gian và chi phí để tái tạo các môi trường giống sản xuất có thể là quá lớn, vì vậy bạn phải thỏa hiệp. Ngoài ra, đôi khi việc sử dụng một môi trường giống sản xuất có thể làm chậm các vòng lặp phản hồi; ví dụ, việc chờ đợi 25 máy cài đặt phần mềm của bạn trong AWS có thể chậm hơn nhiều so với việc chỉ cần triển khai dịch vụ của bạn vào một phiên bản Vagrant cục bộ.

Sự cân bằng này, giữa các môi trường giống sản xuất và phản hồi nhanh, sẽ không tĩnh. Hãy để mắt đến các lỗi bạn tìm thấy ở hạ nguồn và thời gian phản hồi của bạn, và điều chỉnh sự cân bằng này khi cần thiết.

Việc quản lý các môi trường cho các hệ thống `monolithic` một tạo tác có thể là một thách thức, đặc biệt nếu bạn không có quyền truy cập vào các hệ thống có thể tự động hóa dễ dàng. Khi bạn nghĩ về nhiều môi trường cho mỗi microservice, điều này có thể còn đáng sợ hơn. Chúng ta sẽ xem xét ngay sau đây một số nền tảng triển khai khác nhau có thể làm cho điều này dễ dàng hơn nhiều cho chúng ta.

#### **Cấu hình Dịch vụ (Service Configuration)**

Các dịch vụ của chúng ta cần một số cấu hình. Lý tưởng nhất, đây nên là một lượng nhỏ, và giới hạn ở những tính năng thay đổi từ môi trường này sang môi trường khác, chẳng hạn như *tên người dùng và mật khẩu nào tôi nên sử dụng để kết nối với cơ sở dữ liệu của mình?* Cấu hình thay đổi từ môi trường này sang môi trường khác nên được giữ ở mức tối thiểu tuyệt đối. Càng nhiều cấu hình của bạn thay đổi hành vi dịch vụ cơ bản, và càng nhiều cấu hình đó thay đổi từ môi trường này sang môi trường khác, bạn sẽ càng tìm thấy các vấn đề chỉ trong một số môi trường nhất định, điều này cực kỳ đau đớn.

Vì vậy, nếu chúng ta có một số cấu hình cho dịch vụ của mình thay đổi từ môi trường này sang môi trường khác, chúng ta nên xử lý điều này như thế nào như một phần của quy trình triển khai của mình? Một tùy chọn là xây dựng một tạo tác cho mỗi môi trường, với cấu hình bên trong chính tạo tác đó. Ban đầu, điều này có vẻ hợp lý. Cấu hình được tích hợp sẵn; chỉ cần triển khai nó và mọi thứ sẽ hoạt động tốt, phải không? Điều này có vấn đề. Hãy nhớ khái niệm về phân phối liên tục. Chúng ta muốn tạo ra một tạo tác đại diện cho ứng cử viên phát hành của chúng ta, và di chuyển nó qua đường ống của chúng ta, xác nhận rằng nó đủ tốt để đi vào sản xuất. Hãy tưởng tượng tôi xây dựng các tạo tác `Customer-Service-Test` và `Customer-Service-Prod`. Nếu tạo tác `Customer-Service-Test` của tôi vượt qua các bài kiểm thử, nhưng chính tạo tác `Customer-Service-Prod` là cái tôi thực sự triển khai, liệu tôi có thể chắc chắn rằng tôi đã xác minh phần mềm thực sự kết thúc trong sản xuất không?

Cũng có những thách thức khác.
*   **Thứ nhất**, có thêm thời gian để xây dựng những tạo tác này.
*   **Thứ hai**, thực tế là bạn cần biết tại thời điểm xây dựng những môi trường nào tồn tại.
*   **Và làm thế nào để bạn xử lý dữ liệu cấu hình nhạy cảm?** Tôi không muốn thông tin về mật khẩu sản xuất được `check-in` cùng với mã nguồn của mình, nhưng nếu nó cần thiết tại thời điểm xây dựng để tạo ra tất cả những tạo tác đó, điều này thường khó tránh khỏi.

Một cách tiếp cận tốt hơn là tạo ra một tạo tác duy nhất, và quản lý cấu hình riêng biệt. Đây có thể là một tệp thuộc tính tồn tại cho mỗi môi trường, hoặc các tham số khác nhau được truyền vào một quy trình cài đặt. Một tùy chọn phổ biến khác, đặc biệt là khi đối phó với một số lượng lớn hơn các microservice, là sử dụng một hệ thống chuyên dụng để cung cấp cấu hình, mà chúng ta sẽ khám phá nhiều hơn trong **Chương 11**.

#### **Ánh xạ Dịch vụ-đến-Máy chủ (Service-to-Host Mapping)**

Một trong những câu hỏi xuất hiện khá sớm trong cuộc thảo luận về microservices là "Bao nhiêu dịch vụ cho mỗi máy?" Trước khi chúng ta tiếp tục, chúng ta nên chọn một thuật ngữ tốt hơn là *máy*, hoặc thậm chí là *box* chung chung hơn mà tôi đã sử dụng trước đó. Trong thời đại ảo hóa này, sự ánh xạ giữa một **máy chủ (host)** đang chạy một hệ điều hành và cơ sở hạ tầng vật lý cơ bản có thể thay đổi rất nhiều. Do đó, tôi có xu hướng nói về các **máy chủ**, sử dụng chúng như một đơn vị cô lập chung—cụ thể là, một hệ điều hành mà tôi có thể cài đặt và chạy các dịch vụ của mình. Nếu bạn đang triển khai trực tiếp lên các máy vật lý, thì một máy chủ vật lý ánh xạ đến một máy chủ (điều này có lẽ không hoàn toàn đúng về mặt thuật ngữ trong bối cảnh này, nhưng trong trường hợp không có các thuật ngữ tốt hơn có thể phải đủ). Nếu bạn đang sử dụng ảo hóa, một máy vật lý duy nhất có thể ánh xạ đến nhiều máy chủ độc lập, mỗi máy chủ có thể chứa một hoặc nhiều dịch vụ.

Vì vậy, khi suy nghĩ về các mô hình triển khai khác nhau, chúng ta sẽ nói về các máy chủ. Vậy thì, chúng ta nên có bao nhiêu dịch vụ cho mỗi máy chủ?

Tôi có một quan điểm chắc chắn về mô hình nào là thích hợp hơn, nhưng có một số yếu tố cần xem xét khi tìm ra mô hình nào sẽ phù hợp với bạn. Cũng rất quan trọng để hiểu rằng một số lựa chọn chúng ta đưa ra về mặt này sẽ hạn chế một số tùy chọn triển khai có sẵn cho chúng ta.

##### **Nhiều Dịch vụ trên mỗi Máy chủ (Multiple Services Per Host)**

Việc có nhiều dịch vụ trên mỗi máy chủ, như được hiển thị trong Hình 6-6, hấp dẫn vì một số lý do.
*   **Thứ nhất**, hoàn toàn từ quan điểm quản lý máy chủ, nó đơn giản hơn. Trong một thế giới nơi một đội quản lý cơ sở hạ tầng và một đội khác quản lý phần mềm, khối lượng công việc của đội cơ sở hạ tầng thường là một hàm của số lượng máy chủ mà họ phải quản lý. Nếu nhiều dịch vụ được đóng gói trên một máy chủ duy nhất, khối lượng công việc quản lý máy chủ không tăng lên khi số lượng dịch vụ tăng lên.
*   **Thứ hai** là chi phí. Ngay cả khi bạn có quyền truy cập vào một nền tảng ảo hóa cho phép bạn cấp phát và thay đổi kích thước các máy chủ ảo, việc ảo hóa có thể thêm một chi phí làm giảm các tài nguyên cơ bản có sẵn cho các dịch vụ của bạn.

Theo quan điểm của tôi, cả hai vấn đề này đều có thể được giải quyết bằng các phương pháp làm việc mới và công nghệ, và chúng ta sẽ khám phá điều đó ngay sau đây.

Mô hình này cũng quen thuộc với những người triển khai vào một dạng `container` ứng dụng nào đó. Theo một số cách, việc sử dụng một `container` ứng dụng là một trường hợp đặc biệt của mô hình nhiều dịch vụ trên mỗi máy chủ, vì vậy chúng ta sẽ xem xét điều đó riêng. Mô hình này cũng có thể đơn giản hóa cuộc sống của nhà phát triển. Việc triển khai nhiều dịch vụ vào một máy chủ duy nhất trong sản xuất đồng nghĩa với việc triển khai nhiều dịch vụ vào một máy trạm hoặc máy tính xách tay phát triển cục bộ. Nếu chúng ta muốn xem xét một mô hình thay thế, chúng ta muốn tìm một cách để giữ cho điều này đơn giản về mặt khái niệm cho các nhà phát triển.

![alt text](<images/Screenshot from 2025-09-29 13-05-21.png>)

**Hình 6-6.** *Nhiều microservice trên mỗi máy chủ*

Tuy nhiên, có một số thách thức với mô hình này.
*   **Thứ nhất**, nó có thể làm cho việc giám sát trở nên khó khăn hơn. Ví dụ, khi theo dõi CPU, tôi có cần theo dõi CPU của một dịch vụ độc lập với các dịch vụ khác không? Hay tôi quan tâm đến CPU của toàn bộ hộp? Các tác dụng phụ cũng có thể khó tránh khỏi. Nếu một dịch vụ chịu tải đáng kể, nó có thể làm giảm các tài nguyên có sẵn cho các bộ phận khác của hệ thống. Gilt, khi mở rộng quy mô số lượng dịch vụ mà nó chạy, đã gặp phải vấn đề này. Ban đầu, nó cho cùng tồn tại nhiều dịch vụ trên một hộp duy nhất, nhưng tải không đều trên một trong các dịch vụ sẽ có tác động bất lợi đến mọi thứ khác đang chạy trên máy chủ đó. Điều này cũng làm cho việc phân tích tác động của các sự cố máy chủ trở nên phức tạp hơn—việc đưa một máy chủ duy nhất ra khỏi hoạt động có thể có hiệu ứng gợn sóng lớn.
*   **Việc triển khai các dịch vụ** cũng có thể phức tạp hơn một chút, vì việc đảm bảo một lần triển khai không ảnh hưởng đến lần khác dẫn đến những cơn đau đầu bổ sung. Ví dụ, nếu tôi sử dụng Puppet để chuẩn bị một máy chủ, nhưng mỗi dịch vụ có các phụ thuộc khác nhau (và có khả năng mâu thuẫn), làm thế nào để tôi làm cho điều đó hoạt động? Trong trường hợp tồi tệ nhất, tôi đã thấy mọi người ràng buộc việc triển khai nhiều dịch vụ lại với nhau, triển khai nhiều dịch vụ khác nhau vào một máy chủ duy nhất trong một bước, để cố gắng đơn giản hóa việc triển khai nhiều dịch vụ vào một máy chủ. Theo quan điểm của tôi, lợi ích nhỏ trong việc cải thiện sự đơn giản bị vượt qua rất nhiều bởi thực tế là chúng ta đã từ bỏ một trong những lợi ích chính của microservices: phấn đấu cho việc phát hành phần mềm của chúng ta một cách độc lập. Nếu bạn áp dụng mô hình nhiều dịch vụ trên mỗi máy chủ, hãy chắc chắn giữ vững ý tưởng rằng mỗi dịch vụ nên được triển khai một cách độc lập.
*   Mô hình này cũng có thể **cản trở quyền tự chủ của các đội**. Nếu các dịch vụ cho các đội khác nhau được cài đặt trên cùng một máy chủ, ai có quyền cấu hình máy chủ cho các dịch vụ của họ? Rất có thể, điều này cuối cùng sẽ được xử lý bởi một đội ngũ tập trung, có nghĩa là cần nhiều sự phối hợp hơn để triển khai các dịch vụ.
*   Một vấn đề khác là tùy chọn này có thể **hạn chế các tùy chọn tạo tác triển khai của chúng ta**. Các triển khai dựa trên hình ảnh không thể thực hiện được, cũng như các máy chủ bất biến trừ khi bạn ràng buộc nhiều dịch vụ khác nhau lại với nhau trong một tạo tác duy nhất, điều mà chúng ta thực sự muốn tránh.
*   Thực tế là chúng ta có nhiều dịch vụ trên một máy chủ duy nhất có nghĩa là các nỗ lực **nhắm mục tiêu mở rộng quy mô** đến dịch vụ cần nó nhất có thể trở nên phức tạp. Tương tự như vậy, nếu một microservice xử lý dữ liệu và các hoạt động đặc biệt nhạy cảm, chúng ta có thể muốn thiết lập máy chủ cơ bản một cách khác biệt, hoặc có lẽ thậm chí đặt chính máy chủ đó trong một phân đoạn mạng riêng biệt. Việc có mọi thứ trên một máy chủ có nghĩa là chúng ta có thể phải đối xử với tất cả các dịch vụ theo cùng một cách ngay cả khi nhu cầu của chúng khác nhau.

Như đồng nghiệp của tôi, Neal Ford, đã nói, nhiều phương pháp làm việc của chúng ta xung quanh việc triển khai và quản lý máy chủ là một nỗ lực để tối ưu hóa cho sự khan hiếm tài nguyên. Trong quá khứ, tùy chọn duy nhất nếu chúng ta muốn một máy chủ khác là mua hoặc thuê một máy vật lý khác. Điều này thường có thời gian thực hiện dài và dẫn đến một cam kết tài chính dài hạn. Không có gì lạ khi các khách hàng tôi đã làm việc cùng chỉ cấp phát các máy chủ mới mỗi hai đến ba năm một lần, và việc cố gắng có được các máy bổ sung ngoài các mốc thời gian này là rất khó khăn. Nhưng các nền tảng điện toán theo yêu cầu đã giảm đáng kể chi phí của các tài nguyên điện toán, và những cải tiến trong công nghệ ảo hóa có nghĩa là ngay cả đối với cơ sở hạ tầng được lưu trữ nội bộ cũng có nhiều sự linh hoạt hơn.

*(Phần tiếp theo sẽ đi sâu vào "Application Containers" và mô hình "Single Service Per Host".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với phần còn lại của Chương 6, đi sâu vào các mô hình triển khai còn lại và tầm quan trọng của tự động hóa.

---

##### **Container Ứng dụng (Application Containers)**

Nếu bạn quen thuộc với việc triển khai các ứng dụng .NET đằng sau IIS hoặc các ứng dụng Java vào một `servlet container`, bạn sẽ rất quen thuộc với mô hình nơi nhiều dịch vụ hoặc ứng dụng riêng biệt nằm bên trong một `container` ứng dụng duy nhất, mà lần lượt nằm trên một máy chủ duy nhất, như chúng ta thấy trong Hình 6-7. Ý tưởng là `container` ứng dụng mà các dịch vụ của bạn sống trong đó mang lại cho bạn những lợi ích về mặt khả năng quản lý được cải thiện, chẳng hạn như hỗ trợ phân cụm (`clustering`) để nhóm nhiều phiên bản lại với nhau, các công cụ giám sát, và những thứ tương tự.

![alt text](<images/Screenshot from 2025-09-29 13-05-29.png>)

**Hình 6-7.** *Nhiều microservice trên mỗi máy chủ (trong một container ứng dụng)*

Thiết lập này cũng có thể mang lại lợi ích về mặt giảm chi phí của các `runtime` ngôn ngữ. Hãy xem xét việc chạy năm dịch vụ Java trong một `servlet container` Java duy nhất. Tôi chỉ có chi phí của một JVM duy nhất. So sánh điều này với việc chạy năm JVM độc lập trên cùng một máy chủ khi sử dụng các `container` nhúng. Điều đó nói rằng, tôi vẫn cảm thấy rằng các `container` ứng dụng này có đủ nhược điểm mà bạn nên tự thử thách mình để xem liệu chúng có thực sự cần thiết hay không.

Trong số các nhược điểm, đầu tiên là chúng chắc chắn **hạn chế lựa chọn công nghệ**. Bạn phải chấp nhận một `technology stack`. Điều này có thể không chỉ hạn chế các lựa chọn công nghệ cho việc triển khai chính dịch vụ, mà còn cả các tùy chọn bạn có về mặt tự động hóa và quản lý các hệ thống của mình. Như chúng ta sẽ thảo luận ngay sau đây, một trong những cách chúng ta có thể giải quyết chi phí quản lý nhiều máy chủ là xung quanh tự động hóa, và do đó việc hạn chế các tùy chọn của chúng ta để giải quyết vấn đề này có thể gây hại gấp đôi.

Tôi cũng sẽ đặt câu hỏi về một số giá trị của các tính năng `container`. Nhiều trong số chúng quảng cáo khả năng quản lý các cụm để hỗ trợ trạng thái phiên chia sẻ trong bộ nhớ, một điều chúng ta hoàn toàn muốn tránh trong mọi trường hợp do những thách thức mà điều này tạo ra khi mở rộng quy mô các dịch vụ của chúng ta. Và các khả năng giám sát mà chúng cung cấp sẽ không đủ khi chúng ta xem xét các loại giám sát kết hợp mà chúng ta muốn thực hiện trong một thế giới microservices, như chúng ta sẽ thấy trong **Chương 8**. Nhiều trong số chúng cũng có thời gian khởi động khá chậm, ảnh hưởng đến chu kỳ phản hồi của nhà phát triển.

Cũng có những bộ vấn đề khác. Việc cố gắng thực hiện quản lý vòng đời phù hợp của các ứng dụng trên các nền tảng như JVM có thể có vấn đề, và phức tạp hơn so với việc chỉ khởi động lại một JVM. Việc phân tích việc sử dụng tài nguyên và các luồng cũng phức tạp hơn nhiều, vì bạn có nhiều ứng dụng chia sẻ cùng một tiến trình. Và hãy nhớ rằng, ngay cả khi bạn nhận được giá trị từ một `container` cụ thể cho công nghệ, chúng không miễn phí. Ngoài thực tế là nhiều trong số chúng là thương mại và do đó có một hàm ý chi phí, chúng còn thêm một chi phí tài nguyên tự thân.

Cuối cùng, cách tiếp cận này một lần nữa là một nỗ lực để tối ưu hóa cho sự khan hiếm tài nguyên mà đơn giản là có thể không còn tồn tại nữa. Dù bạn quyết định có nhiều dịch vụ trên mỗi máy chủ như một mô hình triển khai, tôi sẽ đề nghị mạnh mẽ xem xét các microservice có thể triển khai độc lập, tự chứa như là các tạo tác. Đối với .NET, điều này có thể thực hiện được với những thứ như Nancy, và Java đã hỗ trợ mô hình này trong nhiều năm. Ví dụ, `container` Jetty nhúng đáng kính tạo nên một máy chủ `HTTP` tự chứa rất nhẹ, là cốt lõi của `stack` Dropwizard. Google đã được biết đến là rất vui vẻ sử dụng các `container` Jetty nhúng để phục vụ nội dung tĩnh trực tiếp, vì vậy chúng ta biết những thứ này có thể hoạt động ở quy mô lớn.

##### **Một Dịch vụ trên mỗi Máy chủ (Single Service Per Host)**

Với mô hình một dịch vụ trên mỗi máy chủ được hiển thị trong Hình 6-8, chúng ta tránh được các tác dụng phụ của việc có nhiều máy chủ sống trên một máy chủ duy nhất, làm cho việc giám sát và khắc phục sự cố đơn giản hơn nhiều. Chúng ta có khả năng đã giảm bớt các điểm lỗi duy nhất của mình. Một sự cố đối với một máy chủ chỉ nên ảnh hưởng đến một dịch vụ duy nhất, mặc dù điều đó không phải lúc nào cũng rõ ràng khi bạn đang sử dụng một nền tảng ảo hóa. Chúng ta sẽ đề cập đến việc thiết kế cho quy mô và lỗi nhiều hơn trong **Chương 11**. Chúng ta cũng có thể dễ dàng mở rộng quy mô một dịch vụ độc lập với các dịch vụ khác, và giải quyết các mối quan tâm về bảo mật dễ dàng hơn bằng cách chỉ tập trung sự chú ý của chúng ta vào dịch vụ và máy chủ yêu cầu nó.

![alt text](<images/Screenshot from 2025-09-29 13-05-36.png>)

**Hình 6-8.** *Một microservice duy nhất trên mỗi máy chủ*

Điều quan trọng không kém là chúng ta đã mở ra tiềm năng sử dụng các kỹ thuật triển khai thay thế như các triển khai dựa trên hình ảnh hoặc mẫu hình máy chủ bất biến, mà chúng ta đã thảo luận trước đó.

Chúng ta đã thêm rất nhiều sự phức tạp trong việc áp dụng một kiến trúc microservice. Điều cuối cùng chúng ta muốn làm là đi tìm kiếm thêm các nguồn phức tạp. Theo quan điểm của tôi, nếu bạn không có một PaaS khả thi, thì mô hình này thực hiện một công việc rất tốt trong việc giảm sự phức tạp tổng thể của một hệ thống. Việc có một mô hình một dịch vụ trên mỗi máy chủ dễ lý giải hơn đáng kể và có thể giúp giảm sự phức tạp. Nếu bạn chưa thể chấp nhận mô hình này, tôi sẽ không nói rằng microservices không dành cho bạn. Nhưng tôi sẽ đề nghị bạn xem xét việc chuyển sang mô hình này theo thời gian như một cách để giảm sự phức tạp mà một kiến trúc microservice có thể mang lại.

Việc có một số lượng máy chủ tăng lên có những nhược điểm tiềm tàng. Chúng ta có nhiều máy chủ hơn để quản lý, và cũng có thể có một hàm ý chi phí của việc chạy nhiều máy chủ riêng biệt hơn. Mặc dù có những vấn đề này, đây vẫn là mô hình tôi ưa thích cho các kiến trúc microservice. Và chúng ta sẽ nói về một vài điều chúng ta có thể làm để giảm chi phí xử lý số lượng lớn máy chủ ngay sau đây.

##### **Nền tảng như một Dịch vụ (Platform as a Service - PaaS)**

Khi sử dụng một **nền tảng như một dịch vụ (Platform as a Service - PaaS)**, bạn đang làm việc ở một mức độ trừu tượng cao hơn so với một máy chủ duy nhất. Hầu hết các nền tảng này dựa vào việc lấy một tạo tác cụ thể cho công nghệ, chẳng hạn như một tệp `WAR` của Java hoặc một `gem` của Ruby, và tự động cấp phát và chạy nó cho bạn. Một số nền tảng này sẽ cố gắng một cách minh bạch xử lý việc mở rộng quy mô hệ thống lên và xuống cho bạn, mặc dù một cách phổ biến hơn (và theo kinh nghiệm của tôi, ít bị lỗi hơn) sẽ cho phép bạn một số quyền kiểm soát về số lượng nút mà dịch vụ của bạn có thể chạy, nhưng nó sẽ xử lý phần còn lại.

Tại thời điểm viết bài, hầu hết các giải pháp PaaS tốt nhất, bóng bẩy nhất đều được lưu trữ. Heroku hiện lên trong tâm trí có lẽ là tiêu chuẩn vàng của PaaS. Nó không chỉ xử lý việc chạy dịch vụ của bạn, nó còn hỗ trợ các dịch vụ như cơ sở dữ liệu một cách rất đơn giản.

Các giải pháp tự lưu trữ có tồn tại trong không gian này, mặc dù chúng còn non nớt hơn các giải pháp được lưu trữ.

Khi các giải pháp PaaS hoạt động tốt, chúng hoạt động rất tốt. Tuy nhiên, khi chúng không hoàn toàn hoạt động tốt cho bạn, bạn thường không có nhiều quyền kiểm soát về việc đi sâu vào bên trong để khắc phục mọi thứ. Đây là một phần của sự đánh đổi bạn thực hiện. Tôi sẽ nói rằng theo kinh nghiệm của tôi, các giải pháp PaaS càng thông minh hơn, chúng càng có nhiều khả năng sai. Tôi đã sử dụng nhiều hơn một PaaS cố gắng tự động mở rộng quy mô dựa trên việc sử dụng ứng dụng, nhưng lại làm điều đó một cách tồi tệ. Các phỏng đoán thúc đẩy những sự thông minh này có xu hướng được điều chỉnh cho ứng dụng trung bình thay vì trường hợp sử dụng cụ thể của bạn. Ứng dụng của bạn càng không chuẩn, khả năng nó không hoạt động tốt với một PaaS càng cao.

Vì các giải pháp PaaS tốt xử lý rất nhiều cho bạn, chúng có thể là một cách tuyệt vời để xử lý chi phí gia tăng mà chúng ta có được khi có nhiều bộ phận chuyển động hơn. Điều đó nói rằng, tôi vẫn không chắc rằng chúng ta đã có tất cả các mô hình đúng trong không gian này, và các tùy chọn tự lưu trữ hạn chế có nghĩa là cách tiếp cận này có thể không hoạt động cho bạn. Tuy nhiên, trong thập kỷ tới, tôi hy vọng chúng ta sẽ nhắm mục tiêu PaaS để triển khai nhiều hơn là phải tự quản lý các máy chủ và triển khai các dịch vụ riêng lẻ.

---
#### **Tự động hóa (Automation)**

Câu trả lời cho rất nhiều vấn đề chúng ta đã nêu ra cho đến nay đều quy về **tự động hóa (automation)**. Với một số lượng nhỏ máy móc, có thể quản lý mọi thứ một cách thủ công. Tôi đã từng làm điều này. Tôi nhớ đã chạy một bộ nhỏ các máy sản xuất, và tôi sẽ thu thập nhật ký, triển khai phần mềm, và kiểm tra các tiến trình bằng cách đăng nhập thủ công vào hộp. Năng suất của tôi dường như bị hạn chế bởi số lượng cửa sổ đầu cuối tôi có thể mở cùng một lúc—một màn hình thứ hai là một bước tiến lớn. Tuy nhiên, điều này nhanh chóng bị phá vỡ.

Một trong những sự phản đối đối với thiết lập một dịch vụ trên mỗi máy chủ là nhận thức rằng lượng chi phí để quản lý các máy chủ này sẽ tăng lên. Điều này chắc chắn đúng nếu bạn đang làm mọi thứ một cách thủ công. Gấp đôi máy chủ, gấp đôi công việc! Nhưng nếu chúng ta tự động hóa việc kiểm soát các máy chủ của mình, và việc triển khai các dịch vụ, thì không có lý do gì việc thêm nhiều máy chủ hơn lại làm tăng khối lượng công việc của chúng ta một cách tuyến tính.

Nhưng ngay cả khi chúng ta giữ số lượng máy chủ nhỏ, chúng ta vẫn sẽ có rất nhiều dịch vụ. Điều đó có nghĩa là nhiều lần triển khai để xử lý, các dịch vụ để giám sát, nhật ký để thu thập. Tự động hóa là điều cần thiết.

Tự động hóa cũng là cách chúng ta có thể đảm bảo rằng các nhà phát triển của chúng ta vẫn làm việc hiệu quả. Việc cho họ khả năng **tự phục vụ (self-service)** cấp phát các dịch vụ riêng lẻ hoặc các nhóm dịch vụ là chìa khóa để làm cho cuộc sống của các nhà phát triển dễ dàng hơn. Lý tưởng nhất, các nhà phát triển nên có quyền truy cập vào chính xác cùng một chuỗi công cụ được sử dụng để triển khai các dịch vụ sản xuất của chúng ta để đảm bảo rằng chúng ta có thể phát hiện các vấn đề sớm. Chúng ta sẽ xem xét rất nhiều công nghệ trong chương này chấp nhận quan điểm này.

##### **Hai Nghiên cứu Điển hình về Sức mạnh của Tự động hóa (Two Case Studies on the Power of Automation)**

Có lẽ sẽ hữu ích nếu cung cấp cho bạn một vài ví dụ cụ thể giải thích sức mạnh của tự động hóa tốt. Một trong những khách hàng của chúng tôi ở Úc là RealEstate.com.au (REA). Trong số những thứ khác, công ty cung cấp danh sách bất động sản cho khách hàng bán lẻ và thương mại ở Úc và những nơi khác trong khu vực Châu Á-Thái Bình Dương. Trong nhiều năm, họ đã chuyển nền tảng của mình theo hướng một thiết kế phân tán, microservices. Khi họ bắt đầu hành trình này, họ đã phải dành rất nhiều thời gian để hoàn thiện các công cụ xung quanh các dịch vụ—làm cho các nhà phát triển dễ dàng cấp phát máy, triển khai mã của họ, hoặc giám sát chúng. Điều này gây ra một công việc tải trước để bắt đầu mọi thứ.

Trong ba tháng đầu tiên của bài tập này, REA đã có thể đưa chỉ hai microservice mới vào sản xuất, với đội ngũ phát triển chịu trách nhiệm hoàn toàn cho toàn bộ việc xây dựng, triển khai và hỗ trợ các dịch vụ. Trong ba tháng tiếp theo, từ 10–15 dịch vụ đã được đưa vào hoạt động theo cách tương tự. Vào cuối giai đoạn 18 tháng, REA đã có hơn 60–70 dịch vụ.

Loại mẫu hình này cũng được chứng thực bởi kinh nghiệm của Gilt, một nhà bán lẻ thời trang trực tuyến bắt đầu vào năm 2007. Ứng dụng Rails `monolithic` của Gilt bắt đầu trở nên khó mở rộng quy mô, và công ty đã quyết định vào năm 2009 bắt đầu phân tách hệ thống thành các microservice. Một lần nữa, tự động hóa, đặc biệt là các công cụ để giúp các nhà phát triển, được đưa ra như một lý do chính để thúc đẩy sự bùng nổ của Gilt trong việc sử dụng microservices. Một năm sau, Gilt có khoảng 10 microservice hoạt động; đến năm 2012, hơn 100; và vào năm 2014, hơn 450 microservice theo thống kê của chính Gilt—nói cách khác, khoảng ba dịch vụ cho mỗi nhà phát triển tại Gilt.

##### **Từ Vật lý đến Ảo (From Physical to Virtual)**

Một trong những công cụ chính có sẵn cho chúng ta trong việc quản lý một số lượng lớn máy chủ là tìm cách chia nhỏ các máy vật lý hiện có thành các phần nhỏ hơn. Ảo hóa truyền thống như VMWare hoặc được AWS sử dụng đã mang lại những lợi ích to lớn trong việc giảm chi phí quản lý máy chủ. Tuy nhiên, đã có một số tiến bộ mới trong không gian này rất đáng để khám phá, vì chúng có thể mở ra những khả năng thú vị hơn nữa để đối phó với kiến trúc microservice của chúng ta.

*(Phần còn lại của chương sẽ đi sâu vào các công nghệ ảo hóa và container hóa cụ thể như `Vagrant`, `Linux Containers`, và `Docker`, cũng như các giao diện triển khai.)*

Chắc chắn rồi! Chúng ta sẽ đi sâu vào các công nghệ ảo hóa và container hóa, những công cụ cực kỳ mạnh mẽ để quản lý môi trường triển khai microservices.

---

##### **Ảo hóa Truyền thống (Traditional Virtualization)**

Tại sao việc có nhiều máy chủ lại tốn kém? Chà, nếu bạn cần một máy chủ vật lý cho mỗi máy chủ, câu trả lời khá rõ ràng. Nếu đây là thế giới bạn đang hoạt động, thì mô hình nhiều dịch vụ trên mỗi máy chủ có lẽ là phù hợp với bạn, mặc dù đừng ngạc nhiên nếu điều này trở thành một ràng buộc ngày càng thách thức. Tuy nhiên, tôi nghi ngờ rằng hầu hết các bạn đang sử dụng một loại ảo hóa nào đó. Ảo hóa cho phép chúng ta chia nhỏ một máy chủ vật lý thành các máy chủ riêng biệt, mỗi máy chủ có thể chạy những thứ khác nhau. Vì vậy, nếu chúng ta muốn một dịch vụ trên mỗi máy chủ, chúng ta không thể chỉ chia nhỏ cơ sở hạ tầng vật lý của mình thành các phần ngày càng nhỏ hơn sao?

Chà, đối với một số người, bạn có thể. Tuy nhiên, việc chia nhỏ một máy thành các máy ảo (`VMs`) ngày càng tăng không phải là miễn phí. Hãy nghĩ về máy vật lý của chúng ta như một ngăn kéo đựng tất. Nếu chúng ta đặt nhiều vách ngăn bằng gỗ vào ngăn kéo của mình, chúng ta có thể lưu trữ nhiều tất hơn hay ít hơn? Câu trả lời là ít hơn: chính các vách ngăn cũng chiếm chỗ! Ngăn kéo của chúng ta có thể dễ dàng đối phó và sắp xếp hơn, và có lẽ chúng ta có thể quyết định đặt áo phông vào một trong các không gian thay vì chỉ là tất, nhưng nhiều vách ngăn hơn có nghĩa là ít không gian tổng thể hơn.

Trong thế giới ảo hóa, chúng ta có một chi phí tương tự như các vách ngăn ngăn kéo của chúng ta. Để hiểu chi phí này đến từ đâu, hãy xem cách hầu hết ảo hóa được thực hiện. Hình 6-9 cho thấy một sự so sánh của hai loại ảo hóa.
*   Bên trái, chúng ta thấy các lớp khác nhau liên quan đến cái được gọi là **ảo hóa loại 2 (type 2 virtualization)**, là loại được triển khai bởi AWS, VMWare, VSphere, Xen, và KVM. (Ảo hóa loại 1 đề cập đến công nghệ nơi các VM chạy trực tiếp trên phần cứng, không phải trên một hệ điều hành khác.)
*   Trên cơ sở hạ tầng vật lý của chúng ta, chúng ta có một hệ điều hành máy chủ. Trên HĐH này, chúng ta chạy một cái gì đó được gọi là **trình ảo hóa (hypervisor)**, có hai công việc chính.
    *   Thứ nhất, nó ánh xạ các tài nguyên như CPU và bộ nhớ từ máy chủ ảo đến máy chủ vật lý.
    *   Thứ hai, nó hoạt động như một lớp điều khiển, cho phép chúng ta thao tác với chính các máy ảo.

![alt text](<images/Screenshot from 2025-09-29 13-05-43.png>)

**Hình 6-9.** *So sánh ảo hóa loại 2 tiêu chuẩn và container nhẹ*

Bên trong các VM, chúng ta có được những gì trông giống như các máy chủ hoàn toàn khác nhau. Chúng có thể chạy các hệ điều hành riêng của chúng, với các `kernel` riêng của chúng. Chúng có thể được coi là các máy gần như được niêm phong kín, được giữ cô lập khỏi máy chủ vật lý cơ bản và các máy ảo khác bởi trình ảo hóa.

Vấn đề là trình ảo hóa ở đây cần phải dành riêng các tài nguyên để thực hiện công việc của mình. Điều này lấy đi CPU, I/O, và bộ nhớ có thể được sử dụng ở nơi khác. Càng nhiều máy chủ mà trình ảo hóa quản lý, nó càng cần nhiều tài nguyên hơn. Tại một thời điểm nhất định, chi phí này trở thành một ràng buộc trong việc chia nhỏ cơ sở hạ tầng vật lý của bạn thêm nữa. Trong thực tế, điều này có nghĩa là thường có lợi nhuận giảm dần trong việc chia nhỏ một hộp vật lý thành các phần ngày càng nhỏ hơn, vì tỷ lệ tài nguyên ngày càng nhiều hơn đi vào chi phí của trình ảo hóa.

##### **Vagrant**

Vagrant là một nền tảng triển khai rất hữu ích, thường được sử dụng cho việc phát triển và kiểm thử hơn là sản xuất. Vagrant cung cấp cho bạn một đám mây ảo trên máy tính xách tay của bạn. Bên dưới, nó sử dụng một hệ thống ảo hóa tiêu chuẩn (thường là VirtualBox, mặc dù nó có thể sử dụng các nền tảng khác). Nó cho phép bạn định nghĩa một tập hợp các VM trong một tệp văn bản, cùng với cách các VM được kết nối mạng với nhau và các hình ảnh mà các VM nên dựa trên. Tệp văn bản này có thể được `check-in` và chia sẻ giữa các thành viên trong đội.

Điều này giúp bạn dễ dàng tạo ra các môi trường giống sản xuất trên máy cục bộ của mình. Bạn có thể khởi động nhiều VM cùng một lúc, tắt các VM riêng lẻ để kiểm tra các chế độ lỗi, và để các VM được ánh xạ qua các thư mục cục bộ để bạn có thể thực hiện các thay đổi và thấy chúng được phản ánh ngay lập tức. Ngay cả đối với các đội sử dụng các nền tảng đám mây theo yêu cầu như AWS, việc quay vòng nhanh hơn của việc sử dụng Vagrant có thể là một lợi ích to lớn cho các đội phát triển.

Tuy nhiên, một trong những nhược điểm là việc chạy nhiều VM có thể làm quá tải máy phát triển trung bình. Nếu chúng ta có một dịch vụ cho một VM, bạn có thể không thể đưa toàn bộ hệ thống của mình lên máy cục bộ của mình. Điều này có thể dẫn đến nhu cầu `stub` ra một số phụ thuộc để làm cho mọi thứ có thể quản lý được, đó là một điều nữa bạn sẽ phải xử lý để đảm bảo trải nghiệm phát triển và kiểm thử là tốt.

##### **Linux Containers**

Đối với người dùng Linux, có một giải pháp thay thế cho ảo hóa. Thay vì có một `hypervisor` để phân đoạn và kiểm soát các máy chủ ảo riêng biệt, các **container Linux** thay vào đó tạo ra một không gian tiến trình riêng biệt trong đó các tiến trình khác sống.

Trên Linux, các tiến trình được chạy bởi một người dùng nhất định, và có các khả năng nhất định dựa trên cách các quyền được thiết lập. Các tiến trình có thể sinh ra các tiến trình khác. Ví dụ, nếu tôi khởi chạy một tiến trình trong một terminal, tiến trình con đó thường được coi là một con của tiến trình terminal. Công việc của `kernel` Linux là duy trì cây tiến trình này.

Các `container` Linux mở rộng ý tưởng này. Mỗi `container` thực chất là một cây con của cây tiến trình hệ thống tổng thể. Các `container` này có thể có các tài nguyên vật lý được phân bổ cho chúng, một điều mà `kernel` xử lý cho chúng ta. Cách tiếp cận chung này đã tồn tại dưới nhiều hình thức, chẳng hạn như Solaris Zones và OpenVZ, nhưng chính **LXC** đã trở nên phổ biến nhất. LXC hiện có sẵn ngay từ đầu trong bất kỳ `kernel` Linux hiện đại nào.

Nếu chúng ta xem xét một sơ đồ `stack` cho một máy chủ đang chạy LXC trong Hình 6-9, chúng ta thấy một vài điểm khác biệt.
*   **Thứ nhất**, chúng ta không cần một `hypervisor`.
*   **Thứ hai**, mặc dù mỗi `container` có thể chạy phân phối hệ điều hành riêng của mình, nó phải chia sẻ cùng một `kernel` (vì `kernel` là nơi cây tiến trình sống). Điều này có nghĩa là hệ điều hành máy chủ của chúng ta có thể chạy Ubuntu, và các `container` của chúng ta CentOS, miễn là cả hai đều có thể chia sẻ cùng một `kernel`.

Chúng ta không chỉ được hưởng lợi từ các tài nguyên được tiết kiệm do không cần một `hypervisor`. Chúng ta cũng có được lợi ích về mặt phản hồi. Các `container` Linux nhanh hơn nhiều để cấp phát so với các máy ảo đầy đủ. Việc một VM mất nhiều phút để khởi động không phải là hiếm—nhưng với các `container` Linux, việc khởi động có thể mất vài giây. Bạn cũng có quyền kiểm soát chi tiết hơn đối với chính các `container` về mặt phân bổ tài nguyên cho chúng, điều này giúp việc tinh chỉnh các cài đặt để tận dụng tối đa phần cứng cơ bản trở nên dễ dàng hơn nhiều.

Do bản chất nhẹ hơn của các `container`, chúng ta có thể có nhiều `container` hơn chạy trên cùng một phần cứng so với những gì có thể với các VM. Bằng cách triển khai một dịch vụ cho mỗi `container`, như trong Hình 6-10, chúng ta có được một mức độ cô lập khỏi các `container` khác (mặc dù điều này không hoàn hảo), và có thể làm điều đó một cách hiệu quả hơn nhiều về mặt chi phí so với những gì có thể nếu chúng ta muốn chạy mỗi dịch vụ trong VM riêng của nó.

![alt text](<images/Screenshot from 2025-09-29 13-05-51.png>)

**Hình 6-10.** *Chạy các dịch vụ trong các container riêng biệt*

Các `container` cũng có thể được sử dụng tốt với ảo hóa đầy đủ. Tôi đã thấy nhiều hơn một dự án cấp phát một phiên bản AWS EC2 lớn và chạy các `container` LXC trên đó để có được điều tốt nhất của cả hai thế giới: một nền tảng điện toán tạm thời, theo yêu cầu dưới dạng EC2, kết hợp với các `container` linh hoạt và nhanh chóng chạy trên đó.

Tuy nhiên, các `container` Linux không phải là không có vấn đề. Hãy tưởng tượng tôi có rất nhiều microservice đang chạy trong các `container` riêng của chúng trên một máy chủ. Thế giới bên ngoài thấy chúng như thế nào? Bạn cần một cách nào đó để định tuyến thế giới bên ngoài vào các `container` cơ bản, một điều mà nhiều `hypervisor` làm cho bạn với ảo hóa thông thường. Tôi đã thấy nhiều người chìm đắm trong một lượng thời gian không tương xứng vào việc cấu hình chuyển tiếp cổng bằng `IPTables` để phơi bày các `container` trực tiếp. Một điểm khác cần lưu ý là các `container` này không thể được coi là hoàn toàn được niêm phong khỏi nhau. Có nhiều cách được ghi nhận và biết đến mà một tiến trình từ một `container` có thể thoát ra và tương tác với các `container` khác hoặc máy chủ cơ bản. Một số vấn đề này là do thiết kế và một số là các lỗi đang được giải quyết, nhưng dù sao đi nữa, nếu bạn không tin tưởng vào mã bạn đang chạy, đừng mong đợi rằng bạn có thể chạy nó trong một `container` và được an toàn. Nếu bạn cần loại cô lập đó, bạn sẽ cần phải xem xét việc sử dụng các máy ảo thay thế.

##### **Docker**

**Docker** là một nền tảng được xây dựng trên các `container` nhẹ. Nó xử lý phần lớn công việc xung quanh việc xử lý các `container` cho bạn. Trong Docker, bạn tạo và triển khai các **ứng dụng**, đồng nghĩa với các hình ảnh trong thế giới VM, mặc dù là cho một nền tảng dựa trên `container`. Docker quản lý việc cấp phát `container`, xử lý một số vấn đề mạng cho bạn, và thậm chí còn cung cấp khái niệm sổ đăng ký (`registry`) riêng cho phép bạn lưu trữ và phiên bản hóa các ứng dụng Docker.

Sự trừu tượng hóa ứng dụng Docker là một điều hữu ích đối với chúng ta, bởi vì giống như với các hình ảnh VM, công nghệ cơ bản được sử dụng để triển khai dịch vụ được ẩn khỏi chúng ta. Chúng ta có các bản dựng cho các dịch vụ của mình tạo ra các ứng dụng Docker, và lưu trữ chúng trong sổ đăng ký Docker, và thế là xong.

Docker cũng có thể giảm bớt một số nhược điểm của việc chạy nhiều dịch vụ cục bộ cho các mục đích phát triển và kiểm thử. Thay vì sử dụng Vagrant để lưu trữ nhiều VM độc lập, mỗi VM chứa dịch vụ riêng của mình, chúng ta có thể lưu trữ một VM duy nhất trong Vagrant chạy một phiên bản Docker. Sau đó, chúng ta sử dụng Vagrant để thiết lập và gỡ bỏ chính nền tảng Docker, và sử dụng Docker để cấp phát nhanh các dịch vụ riêng lẻ.

Một số công nghệ khác nhau đang được phát triển để tận dụng Docker. **CoreOS** là một hệ điều hành rất thú vị được thiết kế với Docker. Nó là một HĐH Linux được rút gọn chỉ cung cấp các dịch vụ thiết yếu để cho phép Docker chạy. Điều này có nghĩa là nó tiêu thụ ít tài nguyên hơn các hệ điều hành khác, giúp có thể dành nhiều tài nguyên hơn của máy cơ bản cho các `container` của chúng ta. Thay vì sử dụng một trình quản lý gói như `debs` hoặc `RPM`, tất cả phần mềm được cài đặt dưới dạng các ứng dụng Docker độc lập, mỗi ứng dụng chạy trong `container` riêng của nó.

Bản thân Docker không giải quyết tất cả các vấn đề cho chúng ta. Hãy nghĩ về nó như một PaaS đơn giản hoạt động trên một máy duy nhất. Nếu bạn muốn các công cụ giúp bạn quản lý các dịch vụ trên nhiều phiên bản Docker trên nhiều máy, bạn sẽ cần phải xem xét các phần mềm khác bổ sung các khả năng này. Có một nhu cầu chính về một lớp **lập lịch (scheduling)** cho phép bạn yêu cầu một `container` và sau đó tìm một `container` Docker có thể chạy nó cho bạn. Trong không gian này, **Kubernetes** được mã nguồn mở gần đây của Google và công nghệ cụm của CoreOS có thể giúp ích, và có vẻ như mỗi tháng lại có một người mới tham gia vào không gian này. **Deis** là một công cụ thú vị khác dựa trên Docker, đang cố gắng cung cấp một PaaS giống Heroku trên Docker.

Tôi đã nói trước đó về các giải pháp PaaS. Cuộc đấu tranh của tôi với chúng luôn là chúng thường nhận sai mức độ trừu tượng, và các giải pháp tự lưu trữ tụt hậu đáng kể so với các giải pháp được lưu trữ như Heroku. Docker làm đúng điều này hơn rất nhiều, và sự bùng nổ quan tâm đến không gian này có nghĩa là tôi nghi ngờ nó sẽ trở thành một nền tảng khả thi hơn nhiều cho tất cả các loại triển khai trong vài năm tới cho tất cả các loại trường hợp sử dụng khác nhau. Theo nhiều cách, Docker với một lớp lập lịch phù hợp nằm giữa IaaS và PaaS—thuật ngữ **containers as a service (CaaS)** đã được sử dụng để mô tả nó.

Docker đang được sử dụng trong sản xuất bởi nhiều công ty. Nó cung cấp nhiều lợi ích của các `container` nhẹ về mặt hiệu quả và tốc độ cấp phát, cùng với các công cụ để tránh nhiều nhược điểm. Nếu bạn quan tâm đến việc xem xét các nền tảng triển khai thay thế, tôi thực sự khuyên bạn nên xem xét Docker.

*(Phần tiếp theo và cuối cùng của chương này sẽ thảo luận về "A Deployment Interface" và "Environment Definition".)*

Chắc chắn rồi! Chúng ta sẽ hoàn thành Chương 6 với phần cuối cùng, tập trung vào việc tạo ra một giao diện triển khai thống nhất và định nghĩa môi trường.

---

#### **Giao diện Triển khai (A Deployment Interface)**

Bất kể nền tảng hoặc tạo tác cơ bản nào bạn sử dụng, việc có một giao diện thống nhất để triển khai một dịch vụ nhất định là rất quan trọng. Chúng ta sẽ muốn kích hoạt việc triển khai một microservice theo yêu cầu trong nhiều tình huống khác nhau, từ việc triển khai cục bộ để phát triển và kiểm thử đến các lần triển khai sản xuất. Chúng ta cũng muốn giữ cho các cơ chế triển khai của mình càng giống nhau càng tốt từ phát triển đến sản xuất, vì điều cuối cùng chúng ta muốn là gặp phải các vấn đề trong sản xuất bởi vì việc triển khai sử dụng một quy trình hoàn toàn khác!

Sau nhiều năm làm việc trong lĩnh vực này, tôi tin rằng cách hợp lý nhất để kích hoạt bất kỳ lần triển khai nào là thông qua một lệnh dòng lệnh duy nhất, có thể tham số hóa. Điều này có thể được kích hoạt bởi các tập lệnh, được khởi chạy bởi công cụ CI của bạn, hoặc được gõ bằng tay. Tôi đã xây dựng các tập lệnh bao bọc (`wrapper scripts`) trong nhiều `technology stack` khác nhau để thực hiện điều này, từ Windows batch, đến bash, đến các tập lệnh Python Fabric, và nhiều hơn nữa, nhưng tất cả các dòng lệnh đều có cùng một định dạng cơ bản.

Chúng ta cần biết chúng ta đang triển khai *cái gì*, vì vậy chúng ta cần cung cấp tên của một thực thể đã biết, hoặc trong trường hợp của chúng ta là một microservice. Chúng ta cũng cần biết *phiên bản* nào của thực thể chúng ta muốn. Câu trả lời cho *phiên bản nào* có xu hướng là một trong ba khả năng.
*   Khi bạn đang làm việc cục bộ, đó sẽ là bất kỳ phiên bản nào có trên máy cục bộ của bạn.
*   Khi kiểm thử, bạn sẽ muốn bản dựng **màu xanh lá cây (green build)** mới nhất, có thể chỉ là tạo tác được chứng nhận gần đây nhất trong kho lưu trữ tạo tác của chúng ta.
*   Hoặc khi kiểm thử/chẩn đoán các vấn đề, chúng ta có thể muốn triển khai một bản dựng chính xác.

Điều thứ ba và cuối cùng chúng ta cần biết là chúng ta muốn microservice được triển khai vào *môi trường* nào. Như chúng ta đã thảo luận trước đó, cấu trúc liên kết (`topology`) của microservice của chúng ta có thể khác nhau từ môi trường này sang môi trường khác, nhưng điều đó nên được ẩn khỏi chúng ta ở đây.

Vì vậy, hãy tưởng tượng chúng ta tạo một tập lệnh triển khai đơn giản lấy ba tham số này. Giả sử chúng ta đang phát triển cục bộ và muốn triển khai dịch vụ danh mục của mình vào môi trường cục bộ của chúng ta. Tôi có thể gõ:
```bash
$ deploy artifact=catalog environment=local version=local
```
Một khi tôi đã `check-in`, dịch vụ xây dựng CI của chúng tôi sẽ nhận thay đổi và tạo ra một tạo tác xây dựng mới, đặt cho nó số hiệu bản dựng `b456`. Như là tiêu chuẩn trong hầu hết các công cụ CI, giá trị này được truyền đi trong suốt đường ống. Khi giai đoạn kiểm thử của chúng ta được kích hoạt, giai đoạn CI sẽ chạy:
```bash
$ deploy artifact=catalog environment=ci version=b456
```
Trong khi đó, QA của chúng ta muốn kéo phiên bản mới nhất của dịch vụ danh mục vào một môi trường kiểm thử tích hợp để thực hiện một số kiểm thử thăm dò, và để giúp cho một buổi giới thiệu. Đội đó chạy:
```bash
$ deploy artifact=catalog environment=integrated_qa version=latest
```
Công cụ mà tôi đã sử dụng nhiều nhất cho việc này là **Fabric**, một thư viện Python được thiết kế để ánh xạ các cuộc gọi dòng lệnh đến các hàm, cùng với sự hỗ trợ tốt cho việc xử lý các tác vụ như `SSH` vào các máy từ xa. Kết hợp nó với một thư viện máy khách AWS như Boto, và bạn có mọi thứ bạn cần để tự động hóa hoàn toàn các môi trường AWS rất lớn. Đối với Ruby, **Capistrano** tương tự theo một số cách với Fabric, và trên Windows, bạn có thể đi một chặng đường dài bằng cách sử dụng PowerShell.

#### **Định nghĩa Môi trường (Environment Definition)**

Rõ ràng, để điều này hoạt động, chúng ta cần có một cách nào đó để định nghĩa các môi trường của chúng ta trông như thế nào, và dịch vụ của chúng ta trông như thế nào trong một môi trường nhất định. Bạn có thể nghĩ về một định nghĩa môi trường như một sự ánh xạ từ một microservice đến các tài nguyên tính toán, mạng và lưu trữ. Tôi đã làm điều này với các tệp YAML trước đây, và đã sử dụng các tập lệnh của mình để kéo dữ liệu này vào. Ví dụ 6-1 là một phiên bản đơn giản hóa của một số công việc tôi đã làm vài năm trước cho một dự án sử dụng AWS.

**Ví dụ 6-1.** *Một ví dụ về định nghĩa môi trường*
```yaml
development:
  nodes:
    - ami_id: ami-e1e1234
      size: t1.micro        # 1
      credentials_name: eu-west-ssh  # 2
      services: [catalog-service]
      region: eu-west-1

production:
  nodes:
    - ami_id: ami-e1e1234
      size: m3.xlarge       # 1
      credentials_name: prod-credentials  # 2
      services: [catalog-service]
      number: 5            # 3
```1.  Chúng tôi đã thay đổi kích thước của các phiên bản chúng tôi đã sử dụng để hiệu quả hơn về chi phí. Bạn không cần một hộp 16 lõi với 64GB RAM cho việc kiểm thử thăm dò!
2.  Việc có thể chỉ định các thông tin đăng nhập khác nhau cho các môi trường khác nhau là chìa khóa. Thông tin đăng nhập cho các môi trường nhạy cảm được lưu trữ trong các kho mã nguồn khác nhau mà chỉ một số người được chọn mới có quyền truy cập.
3.  Chúng tôi đã quyết định rằng theo mặc định, nếu một dịch vụ có nhiều hơn một nút được cấu hình, chúng tôi sẽ tự động tạo ra một bộ cân bằng tải cho nó.

Tôi đã loại bỏ một số chi tiết vì mục đích ngắn gọn.

Thông tin `catalog-service` được lưu trữ ở nơi khác. Nó không khác nhau từ môi trường này sang môi trường khác, như bạn có thể thấy trong Ví dụ 6-2.

**Ví dụ 6-2.** *Một ví dụ về định nghĩa môi trường*
```yaml
catalog-service:
  puppet_manifest : catalog.pp  # 1
  connectivity:
    - protocol: tcp
      ports: [ 8080, 8081 ]
      allowed: [ WORLD ]
```
1.  Đây là tên của tệp Puppet để chạy—chúng tôi tình cờ sử dụng Puppet solo trong tình huống này, nhưng về mặt lý thuyết có thể đã hỗ trợ các hệ thống cấu hình thay thế khác.

Rõ ràng, rất nhiều hành vi ở đây dựa trên quy ước. Ví dụ, chúng tôi đã quyết định chuẩn hóa các cổng mà các dịch vụ sử dụng ở bất cứ đâu chúng chạy, và tự động cấu hình các bộ cân bằng tải nếu một dịch vụ có nhiều hơn một phiên bản (một điều mà các ELB của AWS làm khá dễ dàng).

Việc xây dựng một hệ thống như thế này đòi hỏi một lượng công việc đáng kể. Nỗ lực thường được dồn vào phía trước, nhưng có thể là cần thiết để quản lý sự phức tạp của việc triển khai mà bạn có. Tôi hy vọng trong tương lai bạn sẽ không phải tự mình làm điều này. **Terraform** là một công cụ rất mới từ Hashicorp, hoạt động trong không gian này. Tôi thường sẽ ngại đề cập đến một công cụ mới như vậy trong một cuốn sách thiên về ý tưởng hơn là công nghệ, nhưng nó đang cố gắng tạo ra một công cụ mã nguồn mở theo những dòng này. Bây giờ vẫn còn sớm, nhưng các khả năng của nó đã có vẻ rất thú vị. Với khả năng nhắm mục tiêu các triển khai trên một số nền tảng khác nhau, trong tương lai nó có thể chỉ là công cụ cho công việc.

---
#### **Tóm tắt (Summary)**

Chúng ta đã đề cập rất nhiều ở đây, vì vậy một bản tóm tắt là cần thiết.
*   **Thứ nhất**, hãy tập trung vào việc duy trì khả năng phát hành một dịch vụ một cách độc lập với dịch vụ khác, và đảm bảo rằng bất kỳ công nghệ nào bạn chọn đều hỗ trợ điều này. Tôi rất thích có một kho lưu trữ duy nhất cho mỗi microservice, nhưng tôi còn chắc chắn hơn rằng bạn cần một bản dựng CI cho mỗi microservice nếu bạn muốn triển khai chúng riêng biệt.
*   **Tiếp theo**, nếu có thể, hãy chuyển sang mô hình một dịch vụ trên mỗi máy chủ/container. Hãy xem xét các công nghệ thay thế như LXC hoặc Docker để làm cho việc quản lý các bộ phận chuyển động rẻ hơn và dễ dàng hơn, nhưng hãy hiểu rằng bất kể bạn áp dụng công nghệ nào, một văn hóa tự động hóa là chìa khóa để quản lý mọi thứ. **Tự động hóa mọi thứ**, và nếu công nghệ bạn có không cho phép điều này, hãy tìm một công nghệ mới! Việc có thể sử dụng một nền tảng như AWS sẽ mang lại cho bạn những lợi ích to lớn khi nói đến tự động hóa.
*   Hãy chắc chắn rằng bạn hiểu **tác động của các lựa chọn triển khai của bạn đối với các nhà phát triển**, và đảm bảo họ cảm thấy được yêu thương. Việc tạo ra các công cụ cho phép bạn tự phục vụ triển khai bất kỳ dịch vụ nào vào một số môi trường khác nhau là rất quan trọng, và sẽ giúp các nhà phát triển, người kiểm thử, và những người vận hành.
*   **Cuối cùng**, nếu bạn muốn đi sâu hơn vào chủ đề này, tôi thực sự khuyên bạn nên đọc cuốn *Continuous Delivery* của Jez Humble và David Farley (Addison-Wesley), cuốn sách này đi sâu hơn vào các chủ đề như thiết kế đường ống và quản lý tạo tác.

Trong chương tiếp theo, chúng ta sẽ đi sâu hơn vào một chủ đề mà chúng ta đã đề cập ngắn gọn ở đây. Cụ thể là, làm thế nào để chúng ta kiểm thử các microservice của mình để đảm bảo chúng thực sự hoạt động?

*(Kết thúc Chương 6. Chúng ta sẽ tiếp tục với Chương 7: "Testing".)*

Chắc chắn rồi! Chúng ta sẽ bắt đầu Chương 7: "Kiểm thử (Testing)". Chương này sẽ đi sâu vào các chiến lược và thách thức khi kiểm thử một hệ thống dựa trên microservices.

---

### **7. Kiểm thử (Testing)**

Thế giới kiểm thử tự động đã phát triển đáng kể kể từ khi tôi bắt đầu viết mã, và mỗi tháng dường như lại có một công cụ hoặc kỹ thuật mới nào đó để làm cho nó tốt hơn nữa. Nhưng những thách thức vẫn còn đó về cách kiểm thử chức năng của chúng ta một cách hiệu quả và hiệu suất khi nó trải rộng trên một hệ thống phân tán. Chương này sẽ phân tích các vấn đề liên quan đến việc kiểm thử các hệ thống chi tiết hơn và trình bày một số giải pháp để giúp bạn đảm bảo bạn có thể phát hành chức năng mới của mình một cách tự tin.

Kiểm thử bao gồm rất nhiều lĩnh vực. Ngay cả khi chúng ta chỉ nói về các bài kiểm thử tự động, cũng có một số lượng lớn cần xem xét. Với microservices, chúng ta đã thêm một cấp độ phức tạp khác. Việc hiểu các loại kiểm thử khác nhau mà chúng ta có thể chạy là quan trọng để giúp chúng ta cân bằng các lực lượng đôi khi đối lập giữa việc đưa phần mềm của chúng ta vào sản xuất càng nhanh càng tốt so với việc đảm bảo phần mềm của chúng ta có chất lượng đủ tốt.

#### **Các loại Kiểm thử (Types of Tests)**

Với tư cách là một nhà tư vấn, tôi thích đưa ra các góc phần tư kỳ lạ như một cách để phân loại thế giới, và tôi đã bắt đầu lo lắng rằng cuốn sách này sẽ không có một cái nào. May mắn thay, Brian Marick đã đưa ra một hệ thống phân loại tuyệt vời cho các bài kiểm thử hoàn toàn phù hợp. Hình 7-1 cho thấy một biến thể của góc phần tư của Marick từ cuốn sách *Agile Testing* (Addison-Wesley) của Lisa Crispin và Janet Gregory, giúp phân loại các loại kiểm thử khác nhau.

![alt text](<images/Screenshot from 2025-09-29 13-09-54.png>)

**Hình 7-1.** *Góc phần tư kiểm thử của Brian Marick. Crispin, Lisa; Gregory, Janet, Agile Testing: A Practical Guide for Testers and Agile Teams, Ấn bản thứ nhất, © 2009. Được chuyển thể với sự cho phép của Pearson Education, Inc., Upper Saddle River, NJ.*

*   Ở **phía dưới**, chúng ta có các bài kiểm thử **hướng công nghệ (technology-facing)**—nghĩa là, các bài kiểm thử hỗ trợ các nhà phát triển trong việc tạo ra hệ thống ngay từ đầu. Các bài kiểm thử hiệu suất và các bài kiểm thử đơn vị phạm vi nhỏ rơi vào loại này—tất cả thường được tự động hóa.
*   Điều này được so sánh với **nửa trên** của góc phần tư, nơi các bài kiểm thử giúp các **bên liên quan phi kỹ thuật (nontechnical stakeholders)** hiểu hệ thống của bạn hoạt động như thế nào. Đây có thể là các bài kiểm thử đầu cuối, phạm vi lớn, như được hiển thị trong ô Kiểm thử Chấp nhận (`Acceptance Test`), hoặc kiểm thử thủ công được đặc trưng bởi việc kiểm thử người dùng được thực hiện trên một hệ thống UAT, như được hiển thị trong ô Kiểm thử Thăm dò (`Exploratory Testing`).

Mỗi loại kiểm thử được hiển thị trong góc phần tư này đều có một vị trí. Chính xác bạn muốn thực hiện bao nhiêu bài kiểm thử của mỗi loại sẽ phụ thuộc vào bản chất của hệ thống của bạn, nhưng điểm mấu chốt cần hiểu là bạn có nhiều lựa chọn về cách kiểm thử hệ thống của mình. Xu hướng gần đây đã là tránh bất kỳ loại kiểm thử thủ công quy mô lớn nào, thay vào đó là tự động hóa càng nhiều càng tốt, và tôi hoàn toàn đồng ý với cách tiếp cận này. Nếu bạn hiện đang thực hiện một lượng lớn kiểm thử thủ công, tôi sẽ đề nghị bạn giải quyết vấn đề đó trước khi đi quá xa trên con đường microservices, vì bạn sẽ không nhận được nhiều lợi ích của chúng nếu bạn không thể xác thực phần mềm của mình một cách nhanh chóng và hiệu quả.

Vì mục đích của chương này, chúng ta sẽ bỏ qua kiểm thử thủ công. Mặc dù loại kiểm thử này có thể rất hữu ích và chắc chắn có vai trò của nó, nhưng sự khác biệt với việc kiểm thử một kiến trúc microservice chủ yếu diễn ra trong bối cảnh của các loại kiểm thử tự động khác nhau, vì vậy đó là nơi chúng ta sẽ tập trung thời gian của mình.

Nhưng khi nói đến các bài kiểm thử tự động, chúng ta muốn có bao nhiêu bài của mỗi loại? Một mô hình khác sẽ rất hữu ích để giúp chúng ta trả lời câu hỏi này, và hiểu được những sự đánh đổi khác nhau có thể là gì.

#### **Phạm vi Kiểm thử (Test Scope)**

Trong cuốn sách *Succeeding with Agile* (Addison-Wesley), Mike Cohn đã phác thảo một mô hình được gọi là **Kim tự tháp Kiểm thử (Test Pyramid)** để giúp giải thích các loại kiểm thử tự động bạn cần. Kim tự tháp giúp chúng ta suy nghĩ về các phạm vi mà các bài kiểm thử nên bao quát, nhưng cũng cả tỷ lệ các loại kiểm thử khác nhau mà chúng ta nên hướng tới. Mô hình ban đầu của Cohn đã chia các bài kiểm thử tự động thành **Đơn vị (Unit)**, **Dịch vụ (Service)**, và **Giao diện người dùng (UI)**, mà bạn có thể thấy trong Hình 7-2.

![alt text](<images/Screenshot from 2025-09-29 13-10-02.png>)

**Hình 7-2.** *Kim tự tháp Kiểm thử của Mike Cohn. Cohn, Mike, Succeeding with Agile: Software Development Using Scrum, Ấn bản thứ nhất, © 2010. Được chuyển thể với sự cho phép của Pearson Education, Inc., Upper Saddle River, NJ.*

Vấn đề với mô hình này là tất cả các thuật ngữ này đều có ý nghĩa khác nhau đối với những người khác nhau. "Dịch vụ" đặc biệt bị quá tải, và có nhiều định nghĩa về một bài kiểm thử đơn vị ngoài kia. Một bài kiểm thử có phải là một bài kiểm thử đơn vị nếu tôi chỉ kiểm thử một dòng mã không? Tôi sẽ nói có. Nó có còn là một bài kiểm thử đơn vị nếu tôi kiểm thử nhiều hàm hoặc lớp không? Tôi sẽ nói không, nhưng nhiều người sẽ không đồng ý! Tôi có xu hướng gắn bó với các tên Đơn vị và Dịch vụ mặc dù chúng mơ hồ, nhưng thích gọi các bài kiểm thử UI là **kiểm thử đầu cuối (end-to-end tests)**, điều mà chúng ta sẽ làm từ bây giờ.

Với sự nhầm lẫn này, đáng để chúng ta xem xét các lớp khác nhau này có ý nghĩa gì.

Hãy xem xét một ví dụ thực tế. Trong Hình 7-3, chúng ta có ứng dụng trợ giúp và trang web chính của mình, cả hai đều đang tương tác với dịch vụ khách hàng của chúng ta để truy xuất, xem xét và chỉnh sửa chi tiết khách hàng. Dịch vụ khách hàng của chúng ta lần lượt đang nói chuyện với ngân hàng điểm khách hàng thân thiết của chúng ta, nơi khách hàng của chúng ta tích lũy điểm bằng cách mua đĩa CD của Justin Bieber. Có lẽ. Rõ ràng đây là một lát cắt của hệ thống cửa hàng âm nhạc tổng thể của chúng ta, nhưng đó là một lát cắt đủ tốt để chúng ta đi sâu vào một vài kịch bản khác nhau mà chúng ta có thể muốn kiểm thử.

![alt text](<images/Screenshot from 2025-09-29 13-10-09.png>)

**Hình 7-3.** *Một phần của cửa hàng âm nhạc của chúng ta đang được kiểm thử*

##### **Kiểm thử Đơn vị (Unit Tests)**

Đây là các bài kiểm thử thường kiểm thử một hàm hoặc một lệnh gọi phương thức duy nhất. Các bài kiểm thử được tạo ra như một hiệu ứng phụ của **phát triển hướng kiểm thử (test-driven design - TDD)** sẽ rơi vào loại này, cũng như các loại kiểm thử được tạo ra bởi các kỹ thuật như **kiểm thử dựa trên thuộc tính (property-based testing)**. Chúng ta không khởi chạy các dịch vụ ở đây, và đang hạn chế việc sử dụng các tệp bên ngoài hoặc các kết nối mạng. Nói chung, bạn muốn có một số lượng lớn các loại kiểm thử này. Được thực hiện đúng cách, chúng rất, rất nhanh, và trên phần cứng hiện đại, bạn có thể mong đợi chạy hàng ngàn bài trong vòng chưa đầy một phút.

Đây là các bài kiểm thử giúp chúng ta, các nhà phát triển, và do đó sẽ là **hướng công nghệ**, chứ không phải **hướng nghiệp vụ (business-facing)**, theo thuật ngữ của Marick. Chúng cũng là nơi chúng ta hy vọng sẽ bắt được hầu hết các lỗi của mình. Vì vậy, trong ví dụ của chúng ta, khi chúng ta nghĩ về dịch vụ khách hàng, các bài kiểm thử đơn vị sẽ bao gồm các phần nhỏ của mã một cách cô lập, như được hiển thị trong Hình 7-4.

![alt text](<images/Screenshot from 2025-09-29 13-10-17.png>)

**Hình 7-4.** *Phạm vi của các bài kiểm thử đơn vị trên hệ thống ví dụ của chúng ta*

Mục tiêu chính của các bài kiểm thử này là cung cấp cho chúng ta phản hồi rất nhanh về việc liệu chức năng của chúng ta có tốt hay không. Các bài kiểm thử có thể quan trọng để hỗ trợ việc tái cấu trúc mã, cho phép chúng ta tái cấu trúc mã của mình khi chúng ta đi, biết rằng các bài kiểm thử phạm vi nhỏ của chúng ta sẽ bắt được chúng ta nếu chúng ta mắc lỗi.

##### **Kiểm thử Dịch vụ (Service Tests)**

Các bài kiểm thử dịch vụ được thiết kế để bỏ qua giao diện người dùng và kiểm thử các dịch vụ trực tiếp. Trong một ứng dụng `monolithic`, chúng ta có thể chỉ đang kiểm thử một tập hợp các lớp cung cấp một *dịch vụ* cho giao diện người dùng. Đối với một hệ thống bao gồm một số dịch vụ, một bài kiểm thử dịch vụ sẽ kiểm thử các khả năng của một dịch vụ riêng lẻ.

Lý do chúng ta muốn kiểm thử một dịch vụ duy nhất một mình là để cải thiện sự cô lập của bài kiểm thử để tìm và sửa các vấn đề nhanh hơn. Để đạt được sự cô lập này, chúng ta cần phải **giả lập (stub out)** tất cả các cộng tác viên bên ngoài để chỉ có chính dịch vụ đó nằm trong phạm vi, như Hình 7-5 cho thấy.

![alt text](<images/Screenshot from 2025-09-29 13-10-25.png>)

**Hình 7-5.** *Phạm vi của các bài kiểm thử dịch vụ trên hệ thống ví dụ của chúng ta*

Một số bài kiểm thử này có thể nhanh như các bài kiểm thử nhỏ, nhưng nếu bạn quyết định kiểm thử với một cơ sở dữ liệu thực, hoặc qua mạng đến các cộng tác viên được `stub`, thời gian kiểm thử có thể tăng lên. Chúng cũng bao gồm nhiều phạm vi hơn một bài kiểm thử đơn vị đơn giản, vì vậy khi chúng thất bại, có thể khó phát hiện ra điều gì bị hỏng hơn so với một bài kiểm thử đơn vị. Tuy nhiên, chúng có ít bộ phận chuyển động hơn nhiều và do đó ít mong manh hơn các bài kiểm thử có phạm vi lớn hơn.

##### **Kiểm thử Đầu cuối (End-to-End Tests)**

Các bài kiểm thử đầu cuối là các bài kiểm thử chạy trên toàn bộ hệ thống của bạn. Thường thì chúng sẽ điều khiển một giao diện người dùng đồ họa (GUI) thông qua một trình duyệt, nhưng cũng có thể dễ dàng mô phỏng các loại tương tác người dùng khác, như tải lên một tệp.

Các bài kiểm thử này bao gồm rất nhiều mã sản xuất, như chúng ta thấy trong Hình 7-6. Vì vậy, khi chúng vượt qua, bạn cảm thấy tốt: bạn có một mức độ tự tin cao rằng mã đang được kiểm thử sẽ hoạt động trong sản xuất. Nhưng phạm vi tăng lên này đi kèm với những nhược điểm, và như chúng ta sẽ thấy ngay sau đây, chúng có thể rất khó để thực hiện tốt trong bối cảnh microservices.

![alt text](<images/Screenshot from 2025-09-29 13-10-32.png>)

**Hình 7-6.** *Phạm vi của các bài kiểm thử đầu cuối trên hệ thống ví dụ của chúng ta*

##### **Sự đánh đổi (Trade-Offs)**

Khi bạn đọc kim tự tháp, điều quan trọng cần rút ra là khi bạn đi lên kim tự tháp, phạm vi kiểm thử tăng lên, cũng như sự tự tin của chúng ta rằng chức năng đang được kiểm thử hoạt động. Mặt khác, thời gian chu kỳ phản hồi tăng lên khi các bài kiểm thử mất nhiều thời gian hơn để chạy, và khi một bài kiểm thử thất bại, có thể khó xác định hơn chức năng nào đã bị hỏng. Khi bạn đi xuống kim tự tháp, nói chung các bài kiểm thử trở nên nhanh hơn nhiều, vì vậy chúng ta có được các chu kỳ phản hồi nhanh hơn nhiều. Chúng ta tìm thấy chức năng bị hỏng nhanh hơn, các bản dựng tích hợp liên tục của chúng ta nhanh hơn, và chúng ta ít có khả năng chuyển sang một nhiệm vụ mới trước khi phát hiện ra chúng ta đã làm hỏng một cái gì đó. Khi những bài kiểm thử phạm vi nhỏ hơn đó thất bại, chúng ta cũng có xu hướng biết điều gì đã bị hỏng, thường là chính xác dòng mã nào. Mặt khác, chúng ta không có nhiều tự tin rằng hệ thống của chúng ta nói chung hoạt động nếu chúng ta chỉ kiểm thử một dòng mã!

Khi các bài kiểm thử có phạm vi rộng hơn như dịch vụ hoặc đầu cuối của chúng ta thất bại, chúng ta sẽ cố gắng viết một bài kiểm thử đơn vị nhanh để bắt vấn đề đó trong tương lai. Bằng cách đó, chúng ta liên tục cố gắng cải thiện các chu kỳ phản hồi của mình.

Hầu như mọi đội tôi đã làm việc cùng đều sử dụng các tên khác nhau so với những tên mà Cohn sử dụng trong kim tự tháp. Dù bạn gọi chúng là gì, điều quan trọng cần rút ra là bạn sẽ muốn các bài kiểm thử có phạm vi khác nhau cho các mục đích khác nhau.

*(Phần tiếp theo sẽ là "How Many?" và "Implementing Service Tests".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với phần còn lại của Chương 7, đi sâu vào việc triển khai các loại kiểm thử khác nhau và khám phá một giải pháp thay thế mạnh mẽ cho kiểm thử đầu cuối.

---

##### **Bao nhiêu? (How Many?)**

Vậy nếu tất cả các bài kiểm thử này đều có sự đánh đổi, bạn muốn có bao nhiêu bài của mỗi loại? Một quy tắc kinh nghiệm tốt là bạn có thể sẽ muốn có số lượng bài kiểm thử nhiều hơn theo cấp số nhân khi bạn đi xuống kim tự tháp, nhưng điều quan trọng là biết rằng bạn có các loại kiểm thử tự động khác nhau và hiểu liệu sự cân bằng hiện tại của bạn có gây ra vấn đề cho bạn hay không!

Ví dụ, tôi đã làm việc trên một hệ thống `monolithic`, nơi chúng tôi có 4.000 bài kiểm thử đơn vị, 1.000 bài kiểm thử dịch vụ, và 60 bài kiểm thử đầu cuối. Chúng tôi đã quyết định rằng từ quan điểm của vòng lặp phản hồi, chúng tôi có quá nhiều bài kiểm thử dịch vụ và đầu cuối (loại sau là thủ phạm tồi tệ nhất trong việc ảnh hưởng đến các vòng lặp phản hồi), vì vậy chúng tôi đã làm việc chăm chỉ để thay thế phạm vi bao phủ của bài kiểm thử bằng các bài kiểm thử có phạm vi nhỏ hơn.

Một phản mẫu (`anti-pattern`) phổ biến là cái thường được gọi là **hình nón tuyết kiểm thử (test snow cone)**, hoặc kim tự tháp đảo ngược. Ở đây, có rất ít hoặc không có các bài kiểm thử phạm vi nhỏ, với tất cả phạm vi bao phủ nằm trong các bài kiểm thử có phạm vi lớn. Các dự án này thường có các lần chạy kiểm thử chậm như băng, và các chu kỳ phản hồi rất dài. Nếu các bài kiểm thử này được chạy như một phần của tích hợp liên tục, bạn sẽ không có nhiều bản dựng, và bản chất của thời gian xây dựng có nghĩa là bản dựng có thể bị hỏng trong một thời gian dài khi có điều gì đó bị lỗi.

#### **Triển khai Kiểm thử Dịch vụ (Implementing Service Tests)**

Việc triển khai các bài kiểm thử đơn vị là một công việc khá đơn giản trong kế hoạch tổng thể, và có rất nhiều tài liệu ngoài kia giải thích cách viết chúng. Các bài kiểm thử dịch vụ và đầu cuối là những bài thú vị hơn.

Các bài kiểm thử dịch vụ của chúng tôi muốn kiểm thử một lát cắt chức năng trên toàn bộ dịch vụ, nhưng để cô lập mình khỏi các dịch vụ khác, chúng tôi cần tìm một cách nào đó để `stub` ra tất cả các cộng tác viên của mình. Vì vậy, nếu chúng tôi muốn viết một bài kiểm thử như thế này cho dịch vụ khách hàng từ Hình 7-3, chúng tôi sẽ triển khai một phiên bản của dịch vụ khách hàng, và như đã thảo luận trước đó, chúng tôi sẽ muốn `stub` ra bất kỳ dịch vụ hạ nguồn nào.

Một trong những điều đầu tiên mà bản dựng tích hợp liên tục của chúng tôi sẽ làm là tạo ra một tạo tác nhị phân cho dịch vụ của chúng tôi, vì vậy việc triển khai nó khá đơn giản. Nhưng làm thế nào để chúng ta xử lý việc giả lập các cộng tác viên hạ nguồn?

Bộ kiểm thử dịch vụ của chúng tôi cần phải khởi chạy các dịch vụ `stub` cho bất kỳ cộng tác viên hạ nguồn nào (hoặc đảm bảo chúng đang chạy), và cấu hình dịch vụ đang được kiểm thử để kết nối với các dịch vụ `stub`. Sau đó, chúng tôi cần cấu hình các `stub` để gửi các phản hồi trở lại để bắt chước các dịch vụ trong thế giới thực. Ví dụ, chúng tôi có thể cấu hình `stub` cho ngân hàng điểm khách hàng thân thiết để trả về số dư điểm đã biết cho một số khách hàng nhất định.

##### **Mocking hay Stubbing**

Khi tôi nói về việc `stubbing` các cộng tác viên hạ nguồn, ý tôi là chúng tôi tạo ra một dịch vụ `stub` phản hồi với các câu trả lời được đóng hộp cho các yêu cầu đã biết từ dịch vụ đang được kiểm thử. Ví dụ, tôi có thể nói với ngân hàng điểm `stub` của mình rằng khi được hỏi về số dư của khách hàng 123, nó nên trả về 15.000. Bài kiểm thử không quan tâm liệu `stub` có được gọi 0, 1, hay 100 lần hay không. Một biến thể của điều này là sử dụng một **mock** thay vì một `stub`.

Khi sử dụng một **mock**, tôi thực sự đi xa hơn và đảm bảo cuộc gọi đã được thực hiện. Nếu cuộc gọi mong đợi không được thực hiện, bài kiểm thử sẽ thất bại. Việc triển khai cách tiếp cận này đòi hỏi nhiều sự thông minh hơn trong các cộng tác viên giả mà chúng ta tạo ra, và nếu được sử dụng quá mức có thể khiến các bài kiểm thử trở nên mong manh. Tuy nhiên, như đã lưu ý, một `stub` không quan tâm liệu nó có được gọi 0, 1, hay nhiều lần hay không.

Tuy nhiên, đôi khi các `mock` có thể rất hữu ích để đảm bảo rằng các tác dụng phụ mong đợi xảy ra. Ví dụ, tôi có thể muốn kiểm tra rằng khi tôi tạo một khách hàng, một số dư điểm mới được thiết lập cho khách hàng đó. Sự cân bằng giữa `stubbing` và `mocking` các cuộc gọi là một điều tinh tế, và cũng đầy rẫy những rắc rối trong các bài kiểm thử dịch vụ như trong các bài kiểm thử đơn vị. Tuy nhiên, nói chung, tôi sử dụng các `stub` nhiều hơn nhiều so với các `mock` cho các bài kiểm thử dịch vụ. Để có một cuộc thảo luận sâu hơn về sự đánh đổi này, hãy xem cuốn *Growing Object-Oriented Software, Guided by Tests*, của Steve Freeman và Nat Pryce (Addison-Wesley).

Nói chung, tôi hiếm khi sử dụng các `mock` cho loại kiểm thử này. Nhưng việc có một công cụ có thể làm cả hai là hữu ích.

Trong khi tôi cảm thấy rằng các `stub` và `mock` thực sự được phân biệt khá rõ ràng, tôi biết sự phân biệt có thể gây nhầm lẫn cho một số người, đặc biệt là khi một số người đưa vào các thuật ngữ khác như `fakes`, `spies`, và `dummies`. Martin Fowler gọi tất cả những thứ này, bao gồm cả các `stub` và `mock`, là **nhân đôi kiểm thử (test doubles)**.

##### **Một Dịch vụ Stub Thông minh hơn (A Smarter Stub Service)**

Thông thường đối với các dịch vụ `stub`, tôi đã tự mình xây dựng chúng. Tôi đã sử dụng mọi thứ từ Apache hoặc Nginx đến các `container` Jetty nhúng hoặc thậm chí các máy chủ web Python được khởi chạy từ dòng lệnh để khởi chạy các máy chủ `stub` cho các trường hợp kiểm thử như vậy. Tôi có lẽ đã tái tạo cùng một công việc hết lần này đến lần khác trong việc tạo ra những `stub` này. Đồng nghiệp của tôi tại ThoughtWorks, Brandon Bryars, có khả năng đã cứu nhiều người trong chúng ta một phần công việc với máy chủ `stub/mock` của anh ấy có tên là **Mountebank**.

Bạn có thể nghĩ về Mountebank như một thiết bị phần mềm nhỏ có thể lập trình được qua `HTTP`. Thực tế là nó được viết bằng NodeJS hoàn toàn mờ đục đối với bất kỳ dịch vụ gọi nào. Khi nó khởi chạy, bạn gửi cho nó các lệnh cho nó biết cổng nào để `stub`, giao thức nào để xử lý (hiện tại `TCP`, `HTTP`, và `HTTPS` được hỗ trợ, với nhiều kế hoạch hơn), và những phản hồi nào nó nên gửi khi các yêu cầu được gửi. Nó cũng hỗ trợ việc thiết lập các kỳ vọng nếu bạn muốn sử dụng nó như một `mock`. Bạn có thể thêm hoặc xóa các điểm cuối `stub` này theo ý muốn, giúp một phiên bản Mountebank duy nhất có thể `stub` nhiều hơn một phụ thuộc hạ nguồn.

Vì vậy, nếu chúng ta muốn chạy các bài kiểm thử dịch vụ chỉ cho dịch vụ khách hàng của mình, chúng ta có thể khởi chạy dịch vụ khách hàng, và một phiên bản Mountebank hoạt động như ngân hàng điểm khách hàng thân thiết của chúng ta. Và nếu những bài kiểm thử đó vượt qua, tôi có thể triển khai dịch vụ khách hàng ngay lập tức! Hoặc có thể không? Còn những dịch vụ gọi dịch vụ khách hàng—bộ phận trợ giúp và cửa hàng web thì sao? Chúng ta có biết liệu chúng ta đã thực hiện một thay đổi có thể làm hỏng chúng không? Tất nhiên, chúng ta đã quên các bài kiểm thử quan trọng ở đỉnh kim tự tháp: các bài kiểm thử đầu cuối.

#### **Những Bài kiểm thử Đầu cuối Khó nhằn (Those Tricky End-to-End Tests)**

Trong một hệ thống microservice, các khả năng chúng ta phơi bày thông qua các giao diện người dùng của mình được cung cấp bởi một số dịch vụ. Điểm của các bài kiểm thử đầu cuối như được phác thảo trong kim tự tháp của Mike Cohn là để điều khiển chức năng thông qua các giao diện người dùng này chống lại mọi thứ bên dưới để cung cấp cho chúng ta một cái nhìn tổng quan về một phần lớn hệ thống của chúng ta.

Vì vậy, để thực hiện một bài kiểm thử đầu cuối, chúng ta cần triển khai nhiều dịch vụ cùng nhau, sau đó chạy một bài kiểm thử chống lại tất cả chúng. Rõ ràng, bài kiểm thử này có phạm vi lớn hơn nhiều, dẫn đến sự tự tin hơn rằng hệ thống của chúng ta hoạt động! Mặt khác, các bài kiểm thử này có khả năng chậm hơn và làm cho việc chẩn đoán lỗi trở nên khó khăn hơn. Hãy đi sâu vào chúng một chút nữa bằng cách sử dụng ví dụ trước của chúng ta để xem các bài kiểm thử này có thể phù hợp như thế nào.

Hãy tưởng tượng chúng ta muốn đẩy một phiên bản mới của dịch vụ khách hàng. Chúng ta muốn triển khai các thay đổi của mình vào sản xuất càng sớm càng tốt, nhưng lo ngại rằng chúng ta có thể đã giới thiệu một thay đổi có thể làm hỏng bộ phận trợ giúp hoặc cửa hàng web. Không vấn đề gì—hãy triển khai tất cả các dịch vụ của chúng ta cùng nhau, và chạy một số bài kiểm thử chống lại bộ phận trợ giúp và cửa hàng web để xem liệu chúng ta có gây ra lỗi hay không. Bây giờ một cách tiếp cận ngây thơ sẽ là chỉ cần thêm các bài kiểm thử này vào cuối đường ống dịch vụ khách hàng của chúng ta, như trong Hình 7-7.

![alt text](<images/Screenshot from 2025-09-29 13-10-40.png>)

**Hình 7-7.** *Thêm giai đoạn kiểm thử đầu cuối của chúng ta: cách tiếp cận đúng?*

Cho đến nay thì tốt. Nhưng câu hỏi đầu tiên chúng ta phải tự hỏi là chúng ta nên sử dụng phiên bản nào của các dịch vụ khác? Chúng ta có nên chạy các bài kiểm thử của mình chống lại các phiên bản của bộ phận trợ giúp và cửa hàng web đang ở trong sản xuất không? Đó là một giả định hợp lý, nhưng điều gì sẽ xảy ra nếu có một phiên bản mới của bộ phận trợ giúp hoặc cửa hàng web đang xếp hàng để được phát hành; chúng ta nên làm gì khi đó?

Một vấn đề khác: nếu chúng ta có một bộ kiểm thử dịch vụ khách hàng triển khai nhiều dịch vụ và chạy các bài kiểm thử chống lại chúng, còn các bài kiểm thử đầu cuối mà các dịch vụ khác chạy thì sao? Nếu chúng đang kiểm thử cùng một thứ, chúng ta có thể thấy mình đang bao phủ rất nhiều cùng một nền tảng, và có thể nhân bản rất nhiều nỗ lực để triển khai tất cả các dịch vụ đó ngay từ đầu.

Chúng ta có thể giải quyết cả hai vấn đề này một cách tao nhã bằng cách để nhiều đường ống **hội tụ (fan in)** vào một giai đoạn kiểm thử đầu cuối duy nhất. Ở đây, bất cứ khi nào một bản dựng mới của một trong các dịch vụ của chúng ta được kích hoạt, chúng ta sẽ chạy các bài kiểm thử đầu cuối của mình, một ví dụ mà chúng ta có thể thấy trong Hình 7-8. Một số công cụ CI có hỗ trợ đường ống xây dựng tốt hơn sẽ cho phép các mô hình hội tụ như thế này ngay từ đầu.

![alt text](<images/Screenshot from 2025-09-29 13-10-48.png>)

**Hình 7-8.** *Một cách tiêu chuẩn để xử lý các bài kiểm thử đầu cuối trên các dịch vụ*

Vì vậy, bất cứ lúc nào bất kỳ dịch vụ nào của chúng ta thay đổi, chúng ta sẽ chạy các bài kiểm thử cục bộ cho dịch vụ đó. Nếu những bài kiểm thử đó vượt qua, chúng ta sẽ kích hoạt các bài kiểm thử tích hợp của mình. Tuyệt vời, phải không? Chà, có một vài vấn đề.

##### **Nhược điểm của Kiểm thử Đầu cuối (Downsides to End-to-End Testing)**

Thật không may, có nhiều nhược điểm đối với việc kiểm thử đầu cuối.

##### **Các Bài kiểm thử Không ổn định và Mong manh (Flaky and Brittle Tests)**

Khi phạm vi kiểm thử tăng lên, số lượng các bộ phận chuyển động cũng vậy. Những bộ phận chuyển động này có thể gây ra các lỗi kiểm thử không cho thấy rằng chức năng đang được kiểm thử bị hỏng, mà là một vấn đề khác đã xảy ra. Ví dụ, nếu chúng ta có một bài kiểm thử để xác minh rằng chúng ta có thể đặt hàng một đĩa CD duy nhất, nhưng chúng ta đang chạy bài kiểm thử đó chống lại bốn hoặc năm dịch vụ, nếu bất kỳ dịch vụ nào trong số đó bị lỗi, chúng ta có thể nhận được một lỗi không liên quan gì đến bản chất của chính bài kiểm thử. Tương tự như vậy, một sự cố mạng tạm thời có thể khiến một bài kiểm thử thất bại mà không nói bất cứ điều gì về chức năng đang được kiểm thử.

Càng nhiều bộ phận chuyển động, các bài kiểm thử của chúng ta có thể càng mong manh hơn, và càng ít xác định hơn. Nếu bạn có các bài kiểm thử đôi khi thất bại, nhưng mọi người chỉ chạy lại chúng vì chúng có thể sẽ vượt qua lần sau, thì bạn có các **bài kiểm thử không ổn định (flaky tests)**. Không chỉ các bài kiểm thử bao gồm nhiều quy trình khác nhau là thủ phạm ở đây. Các bài kiểm thử bao gồm chức năng được thực hiện trên nhiều luồng thường có vấn đề, nơi một lỗi có thể có nghĩa là một điều kiện tranh đua (`race condition`), một thời gian chờ, hoặc chức năng thực sự bị hỏng. Các bài kiểm thử không ổn định là kẻ thù. Khi chúng thất bại, chúng không cho chúng ta biết nhiều. Chúng ta chạy lại các bản dựng CI của mình với hy vọng rằng chúng sẽ vượt qua lần sau, chỉ để thấy các `check-in` chồng chất lên nhau, và đột nhiên chúng ta thấy mình với một đống chức năng bị hỏng.

Khi chúng ta phát hiện ra các bài kiểm thử không ổn định, điều cần thiết là chúng ta phải cố gắng hết sức để loại bỏ chúng. Nếu không, chúng ta bắt đầu mất niềm tin vào một bộ kiểm thử "luôn thất bại như vậy." Một bộ kiểm thử với các bài kiểm thử không ổn định có thể trở thành nạn nhân của cái mà Diane Vaughan gọi là **sự bình thường hóa của sự sai lệch (normalization of deviance)**—ý tưởng rằng theo thời gian chúng ta có thể trở nên quá quen với những điều sai trái đến mức chúng ta bắt đầu chấp nhận chúng là bình thường và không phải là một vấn đề. Xu hướng rất con người này có nghĩa là chúng ta cần phải tìm và loại bỏ những bài kiểm thử này càng sớm càng tốt trước khi chúng ta bắt đầu giả định rằng các bài kiểm thử thất bại là OK.

Trong bài viết "Eradicating Non-Determinism in Tests", Martin Fowler ủng hộ cách tiếp cận rằng nếu bạn có các bài kiểm thử không ổn định, bạn nên theo dõi chúng và nếu bạn không thể sửa chúng ngay lập tức, hãy loại bỏ chúng khỏi bộ kiểm thử để bạn có thể xử lý chúng. Xem liệu bạn có thể viết lại chúng để tránh kiểm thử mã chạy trên nhiều luồng không. Xem liệu bạn có thể làm cho môi trường cơ bản ổn định hơn không. Tốt hơn nữa, hãy xem liệu bạn có thể thay thế bài kiểm thử không ổn định bằng một bài kiểm thử có phạm vi nhỏ hơn ít có khả năng thể hiện các vấn đề không. Trong một số trường hợp, việc thay đổi phần mềm đang được kiểm thử để làm cho nó dễ kiểm thử hơn cũng có thể là con đường đúng đắn.

*(Phần tiếp theo sẽ đi sâu hơn vào các vấn đề của kiểm thử đầu cuối, bao gồm quyền sở hữu, thời gian thực hiện, và các giải pháp thay thế.)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục đi sâu vào những thách thức của kiểm thử đầu cuối và khám phá các giải pháp thay thế hiệu quả hơn.

---

##### **Ai Viết những Bài kiểm thử này? (Who Writes These Tests?)**

Với các bài kiểm thử chạy như một phần của đường ống cho một dịch vụ cụ thể, điểm khởi đầu hợp lý là đội sở hữu dịch vụ đó nên viết những bài kiểm thử đó (chúng ta sẽ nói nhiều hơn về quyền sở hữu dịch vụ trong **Chương 10**). Nhưng nếu chúng ta xem xét rằng chúng ta có thể có nhiều đội tham gia, và bước kiểm thử đầu cuối hiện đang được chia sẻ hiệu quả giữa các đội, ai viết và chăm sóc những bài kiểm thử này?

Tôi đã thấy một số phản mẫu (`anti-patterns`) gây ra ở đây. Những bài kiểm thử này trở thành một cuộc chiến tự do, với tất cả các đội được cấp quyền truy cập để thêm các bài kiểm thử mà không có bất kỳ sự hiểu biết nào về sức khỏe của toàn bộ bộ kiểm thử. Điều này thường có thể dẫn đến một sự bùng nổ các trường hợp kiểm thử, đôi khi dẫn đến hình nón tuyết kiểm thử mà chúng ta đã nói trước đó. Tôi đã thấy các tình huống mà, bởi vì không có quyền sở hữu rõ ràng thực sự của những bài kiểm thử này, kết quả của chúng bị bỏ qua. Khi chúng bị hỏng, mọi người đều cho rằng đó là vấn đề của người khác, vì vậy họ không quan tâm liệu các bài kiểm thử có vượt qua hay không.

Đôi khi các tổ chức phản ứng bằng cách có một đội chuyên dụng viết những bài kiểm thử này. Điều này có thể là một thảm họa. Đội phát triển phần mềm ngày càng trở nên xa rời các bài kiểm thử cho mã của mình. Thời gian chu kỳ tăng lên, khi các chủ sở hữu dịch vụ cuối cùng phải chờ đội kiểm thử viết các bài kiểm thử đầu cuối cho chức năng họ vừa viết. Bởi vì một đội khác viết những bài kiểm thử này, đội đã viết dịch vụ ít tham gia hơn, và do đó ít có khả năng biết cách chạy và sửa những bài kiểm thử này. Mặc dù nó vẫn là một mẫu hình tổ chức phổ biến, tôi thấy tác hại đáng kể được gây ra bất cứ khi nào một đội bị tách rời khỏi việc viết các bài kiểm thử cho mã mà họ đã viết ngay từ đầu.

Việc làm đúng khía cạnh này thực sự khó. Chúng ta không muốn nhân đôi nỗ lực, cũng như không muốn tập trung hóa hoàn toàn điều này đến mức các đội xây dựng dịch vụ quá xa rời mọi thứ. Sự cân bằng tốt nhất mà tôi đã tìm thấy là coi bộ kiểm thử đầu cuối như một `codebase` được chia sẻ, nhưng với quyền sở hữu chung. Các đội có thể tự do `check-in` vào bộ kiểm thử này, nhưng quyền sở hữu sức khỏe của bộ kiểm thử phải được chia sẻ giữa các đội đang phát triển chính các dịch vụ đó. Nếu bạn muốn sử dụng rộng rãi các bài kiểm thử đầu cuối với nhiều đội, tôi nghĩ cách tiếp cận này là cần thiết, nhưng tôi đã thấy nó được thực hiện rất hiếm khi, và không bao giờ không có vấn đề.

##### **Mất bao lâu? (How Long?)**

Các bài kiểm thử đầu cuối này có thể mất một thời gian. Tôi đã thấy chúng mất đến một ngày để chạy, nếu không muốn nói là nhiều hơn, và trên một dự án tôi đã làm việc, một bộ hồi quy đầy đủ mất sáu tuần! Tôi hiếm khi thấy các đội thực sự quản lý các bộ kiểm thử đầu cuối của họ để giảm sự chồng chéo trong phạm vi kiểm thử, hoặc dành đủ thời gian để làm cho chúng nhanh chóng.

Sự chậm chạp này, kết hợp với thực tế là các bài kiểm thử này thường có thể không ổn định, có thể là một vấn đề lớn. Một bộ kiểm thử mất cả ngày và thường có các sự cố không liên quan gì đến chức năng bị hỏng là một thảm họa. Ngay cả khi chức năng của bạn bị hỏng, bạn có thể mất nhiều giờ để tìm ra—tại thời điểm đó, nhiều người trong chúng ta đã chuyển sang các hoạt động khác, và việc chuyển đổi bối cảnh trong bộ não của chúng ta trở lại để khắc phục sự cố là rất đau đớn.

Chúng ta có thể cải thiện một phần điều này bằng cách chạy các bài kiểm thử song song—ví dụ, sử dụng các công cụ như Selenium Grid. Tuy nhiên, cách tiếp cận này không phải là một sự thay thế cho việc thực sự hiểu những gì cần được kiểm thử và tích cực loại bỏ các bài kiểm thử không còn cần thiết nữa.

Việc loại bỏ các bài kiểm thử đôi khi là một bài tập đầy khó khăn, và tôi nghi ngờ có nhiều điểm chung với những người muốn loại bỏ một số biện pháp an ninh sân bay. Bất kể các biện pháp an ninh có thể không hiệu quả đến đâu, bất kỳ cuộc trò chuyện nào về việc loại bỏ chúng thường bị phản bác bằng những phản ứng giật đầu gối về việc không quan tâm đến sự an toàn của mọi người hoặc muốn những kẻ khủng bố chiến thắng. Rất khó để có một cuộc trò chuyện cân bằng về giá trị mà một cái gì đó mang lại so với gánh nặng mà nó gây ra. Nó cũng có thể là một sự đánh đổi rủi ro/phần thưởng khó khăn. Bạn có được cảm ơn nếu bạn loại bỏ một bài kiểm thử không? Có thể. Nhưng bạn chắc chắn sẽ bị đổ lỗi nếu một bài kiểm thử bạn đã loại bỏ để lọt một lỗi. Tuy nhiên, khi nói đến các bộ kiểm thử có phạm vi lớn hơn, đây chính xác là những gì chúng ta cần có thể làm. Nếu cùng một tính năng được bao phủ trong 20 bài kiểm thử khác nhau, có lẽ chúng ta có thể loại bỏ một nửa trong số chúng, vì 20 bài kiểm thử đó mất 10 phút để chạy! Điều này đòi hỏi một sự hiểu biết tốt hơn về rủi ro, một điều mà con người nổi tiếng là tệ. Kết quả là, việc quản lý và điều hành thông minh các bài kiểm thử có phạm vi lớn, gánh nặng cao này xảy ra cực kỳ không thường xuyên. Việc ước mọi người làm điều này nhiều hơn không giống như việc làm cho nó xảy ra.

##### **Sự Tồn đọng Lớn (The Great Pile-up)**

Các chu kỳ phản hồi dài liên quan đến các bài kiểm thử đầu cuối không chỉ là một vấn đề khi nói đến năng suất của nhà phát triển. Với một bộ kiểm thử dài, bất kỳ sự cố nào cũng mất một thời gian để khắc phục, điều này làm giảm lượng thời gian mà các bài kiểm thử đầu cuối có thể được mong đợi là sẽ vượt qua. Nếu chúng ta chỉ triển khai phần mềm đã vượt qua tất cả các bài kiểm thử của chúng ta thành công (điều chúng ta nên làm!), điều này có nghĩa là ít dịch vụ của chúng ta vượt qua được đến điểm có thể triển khai vào sản xuất.

Điều này có thể dẫn đến một sự tồn đọng. Trong khi một giai đoạn kiểm thử tích hợp bị hỏng đang được sửa chữa, nhiều thay đổi hơn từ các đội ở thượng nguồn có thể đổ vào. Ngoài việc điều này có thể làm cho việc sửa bản dựng trở nên khó khăn hơn, nó có nghĩa là phạm vi của các thay đổi cần được triển khai tăng lên. Một cách để giải quyết vấn đề này là không cho mọi người `check-in` nếu các bài kiểm thử đầu cuối đang thất bại, nhưng với thời gian chạy bộ kiểm thử dài, điều này thường không thực tế. Hãy thử nói, "30 nhà phát triển các bạn: không `check-in` cho đến khi chúng tôi sửa xong bản dựng dài bảy giờ này!"

Phạm vi của một đợt triển khai càng lớn và rủi ro của một bản phát hành càng cao, chúng ta càng có nhiều khả năng làm hỏng một cái gì đó. Một động lực chính để đảm bảo chúng ta có thể phát hành phần mềm của mình thường xuyên là dựa trên ý tưởng rằng chúng ta phát hành các thay đổi nhỏ ngay khi chúng sẵn sàng.

##### **Siêu phiên bản (The Metaversion)**

Với bước kiểm thử đầu cuối, rất dễ bắt đầu suy nghĩ, *Vậy là, tôi biết tất cả các dịch vụ này ở các phiên bản này hoạt động cùng nhau, vậy tại sao không triển khai tất cả chúng cùng nhau?* Điều này rất nhanh chóng trở thành một cuộc trò chuyện theo hướng, *Vậy tại sao không sử dụng một số phiên bản cho toàn bộ hệ thống?* Để trích dẫn Brandon Bryars, "Bây giờ bạn có các vấn đề của phiên bản 2.1.0."

Bằng cách phiên bản hóa cùng nhau các thay đổi được thực hiện cho nhiều dịch vụ, chúng ta thực chất chấp nhận ý tưởng rằng việc thay đổi và triển khai nhiều dịch vụ cùng một lúc là có thể chấp nhận được. Nó trở thành tiêu chuẩn, nó trở thành OK. Khi làm như vậy, chúng ta từ bỏ một trong những lợi thế chính của microservices: khả năng triển khai một dịch vụ một mình, độc lập với các dịch vụ khác.

Tất cả quá thường xuyên, cách tiếp cận chấp nhận nhiều dịch vụ được triển khai cùng nhau trôi dạt vào một tình huống nơi các dịch vụ trở nên phụ thuộc. Chẳng bao lâu sau, các dịch vụ được tách rời một cách độc lập lại trở nên ngày càng rối rắm với những dịch vụ khác, và bạn không bao giờ nhận thấy vì bạn không bao giờ cố gắng triển khai chúng một mình. Bạn kết thúc với một mớ hỗn độn rối rắm nơi bạn phải điều phối việc triển khai nhiều dịch vụ cùng một lúc, và như chúng ta đã thảo luận trước đây, loại ghép nối này có thể để lại chúng ta ở một nơi tồi tệ hơn so với khi chúng ta có một ứng dụng `monolithic` duy nhất.

Điều này là xấu.

##### **Kiểm thử các Hành trình, không phải các Câu chuyện (Test Journeys, Not Stories)**

Mặc dù có những nhược điểm vừa nêu, đối với nhiều người dùng, các bài kiểm thử đầu cuối vẫn có thể quản lý được với một hoặc hai dịch vụ, và trong những tình huống này vẫn rất có ý nghĩa. Nhưng điều gì sẽ xảy ra với 3, 4, 10, hoặc 20 dịch vụ? Rất nhanh chóng, các bộ kiểm thử này trở nên cực kỳ cồng kềnh, và trong trường hợp tồi tệ nhất có thể dẫn đến một sự bùng nổ kiểu Cartesian trong các kịch bản đang được kiểm thử.

Tình hình này trở nên tồi tệ hơn nếu chúng ta rơi vào bẫy thêm một bài kiểm thử đầu cuối mới cho mỗi phần chức năng chúng ta thêm vào. Hãy cho tôi xem một `codebase` nơi mỗi `story` mới dẫn đến một bài kiểm thử đầu cuối mới, và tôi sẽ cho bạn thấy một bộ kiểm thử cồng kềnh có các chu kỳ phản hồi kém và sự chồng chéo rất lớn trong phạm vi kiểm thử.

Cách tốt nhất để chống lại điều này là tập trung vào một số lượng nhỏ các **hành trình cốt lõi (core journeys)** để kiểm thử cho toàn bộ hệ thống. Bất kỳ chức năng nào không được bao phủ trong các hành trình cốt lõi này cần phải được bao phủ trong các bài kiểm thử phân tích các dịch vụ một cách cô lập khỏi nhau. Các hành trình này cần được thỏa thuận chung, và được sở hữu chung. Đối với cửa hàng âm nhạc của chúng ta, chúng ta có thể tập trung vào các hành động như đặt hàng một đĩa CD, trả lại một sản phẩm, hoặc có lẽ là tạo một khách hàng mới—những tương tác có giá trị cao và số lượng rất ít.

Bằng cách tập trung vào một số lượng nhỏ (và ý tôi là *nhỏ*: số lượng rất thấp hai chữ số ngay cả đối với các hệ thống phức tạp) các bài kiểm thử, chúng ta có thể giảm bớt những nhược điểm của các bài kiểm thử tích hợp, nhưng chúng ta không thể tránh được tất cả chúng. Có cách nào tốt hơn không?

#### **Kiểm thử do Người tiêu dùng Điều khiển để Giải cứu (Consumer-Driven Tests to the Rescue)**

Một trong những vấn đề chính mà chúng ta đang cố gắng giải quyết khi chúng ta sử dụng các bài kiểm thử tích hợp được nêu trước đây là gì? Chúng ta đang cố gắng đảm bảo rằng khi chúng ta triển khai một dịch vụ mới vào sản xuất, các thay đổi của chúng ta sẽ không làm hỏng người tiêu dùng. Một cách chúng ta có thể làm điều này mà không cần kiểm thử chống lại người tiêu dùng thực sự là bằng cách sử dụng một **hợp đồng do người tiêu dùng điều khiển (consumer-driven contract - CDC)**.

Với CDC, chúng ta đang định nghĩa các kỳ vọng của một **người tiêu dùng (consumer)** đối với một dịch vụ (hoặc **nhà sản xuất (producer)**). Các kỳ vọng của người tiêu dùng được ghi lại dưới dạng các bài kiểm thử, sau đó được chạy chống lại nhà sản xuất. Nếu được thực hiện đúng, các CDC này nên được chạy như một phần của bản dựng CI của nhà sản xuất, đảm bảo rằng nó không bao giờ được triển khai nếu nó phá vỡ một trong những hợp đồng này. Rất quan trọng từ quan điểm của phản hồi kiểm thử, các bài kiểm thử này chỉ cần được chạy chống lại một nhà sản xuất duy nhất một cách cô lập, vì vậy có thể nhanh hơn và đáng tin cậy hơn các bài kiểm thử đầu cuối mà chúng có thể thay thế.

Ví dụ, hãy xem lại kịch bản dịch vụ khách hàng của chúng ta. Dịch vụ khách hàng có hai người tiêu dùng riêng biệt: bộ phận trợ giúp và cửa hàng web. Cả hai dịch vụ tiêu thụ này đều có những kỳ vọng về cách dịch vụ khách hàng sẽ hoạt động. Trong ví dụ này, bạn tạo ra hai bộ kiểm thử: một cho mỗi người tiêu dùng đại diện cho việc sử dụng dịch vụ khách hàng của bộ phận trợ giúp và cửa hàng web. Một thực hành tốt ở đây là có ai đó từ các đội nhà sản xuất và người tiêu dùng hợp tác tạo ra các bài kiểm thử, vì vậy có lẽ những người từ các đội cửa hàng web và bộ phận trợ giúp sẽ lập trình cặp với những người từ đội dịch vụ khách hàng.

Bởi vì các CDC này là những kỳ vọng về cách dịch vụ khách hàng nên hoạt động, chúng có thể được chạy chống lại dịch vụ khách hàng một mình với bất kỳ phụ thuộc hạ nguồn nào của nó được `stub` ra, như Hình 7-9 cho thấy. Từ quan điểm phạm vi, chúng nằm ở cùng một cấp độ trong kim tự tháp kiểm thử như các bài kiểm thử dịch vụ, mặc dù với một trọng tâm rất khác, như được hiển thị trong Hình 7-10. Các bài kiểm thử này tập trung vào cách một người tiêu dùng sẽ sử dụng dịch vụ, và yếu tố kích hoạt nếu chúng bị hỏng rất khác so với các bài kiểm thử dịch vụ. Nếu một trong những CDC này bị hỏng trong quá trình xây dựng dịch vụ khách hàng, sẽ rõ ràng người tiêu dùng nào sẽ bị ảnh hưởng. Tại thời điểm này, bạn có thể hoặc là khắc phục sự cố hoặc bắt đầu cuộc thảo luận về việc giới thiệu một thay đổi gây đổ vỡ theo cách chúng ta đã thảo luận trong **Chương 4**. Vì vậy, với CDC, chúng ta có thể xác định một thay đổi gây đổ vỡ trước khi phần mềm của chúng ta đi vào sản xuất mà không cần phải sử dụng một bài kiểm thử đầu cuối có khả năng tốn kém.

![alt text](<images/Screenshot from 2025-09-29 13-10-54.png>)

**Hình 7-9.** *Kiểm thử do người tiêu dùng điều khiển trong bối cảnh của ví dụ dịch vụ khách hàng của chúng ta*

![alt text](<images/Screenshot from 2025-09-29 13-11-02.png>)

**Hình 7-10.** *Tích hợp các bài kiểm thử do người tiêu dùng điều khiển vào kim tự tháp kiểm thử*

##### **Pact**

**Pact** là một công cụ kiểm thử do người tiêu dùng điều khiển ban đầu được phát triển nội bộ tại RealEstate.com.au, nhưng hiện là mã nguồn mở, với Beth Skurrie là người thúc đẩy phần lớn sự phát triển. Ban đầu chỉ dành cho Ruby, Pact hiện bao gồm các cổng JVM và .NET.

Pact hoạt động theo một cách rất thú vị, như được tóm tắt trong Hình 7-11. Người tiêu dùng bắt đầu bằng cách định nghĩa các kỳ vọng của nhà sản xuất bằng cách sử dụng một `DSL` Ruby. Sau đó, bạn khởi chạy một máy chủ `mock` cục bộ, và chạy kỳ vọng này chống lại nó để tạo ra tệp đặc tả Pact. Tệp Pact chỉ là một đặc tả `JSON` chính thức; bạn rõ ràng có thể viết tay những thứ này, nhưng việc sử dụng API ngôn ngữ dễ dàng hơn nhiều. Điều này cũng cung cấp cho bạn một máy chủ `mock` đang chạy có thể được sử dụng cho các bài kiểm thử cô lập hơn nữa của người tiêu dùng.

![alt text](<images/Screenshot from 2025-09-29 13-11-10.png>)

**Hình 7-11.** *Tổng quan về cách Pact thực hiện kiểm thử do người tiêu dùng điều khiển*

Ở phía nhà sản xuất, sau đó bạn xác minh rằng đặc tả người tiêu dùng này được đáp ứng bằng cách sử dụng đặc tả JSON Pact để điều khiển các cuộc gọi chống lại API của bạn và xác minh các phản hồi. Để điều này hoạt động, `codebase` của nhà sản xuất cần quyền truy cập vào tệp Pact. Như chúng ta đã thảo luận trước đó trong **Chương 6**, chúng ta mong đợi cả người tiêu dùng và nhà sản xuất đều ở trong các bản dựng khác nhau. Việc sử dụng một đặc tả `JSON` không phụ thuộc vào ngôn ngữ là một nét chấm phá đặc biệt hay. Điều đó có nghĩa là bạn có thể tạo ra đặc tả của người tiêu dùng bằng cách sử dụng một máy khách Ruby, nhưng sử dụng nó để xác minh một nhà sản xuất Java bằng cách sử dụng cổng JVM của Pact.

Khi đặc tả JSON Pact được tạo ra bởi người tiêu dùng, điều này cần phải trở thành một tạo tác mà bản dựng của nhà sản xuất có quyền truy cập. Bạn có thể lưu trữ điều này trong kho lưu trữ tạo tác của công cụ CI/CD của mình, hoặc sử dụng **Pact Broker**, cho phép bạn lưu trữ nhiều phiên bản của các đặc tả Pact của mình. Điều này có thể cho phép bạn chạy các bài kiểm thử hợp đồng do người tiêu dùng điều khiển của mình chống lại nhiều phiên bản khác nhau của người tiêu dùng, nếu bạn muốn kiểm thử chống lại, chẳng hạn, phiên bản của người tiêu dùng trong sản xuất và phiên bản của người tiêu dùng được xây dựng gần đây nhất.

Thật khó hiểu, có một dự án mã nguồn mở của ThoughtWorks có tên là **Pacto**, cũng là một công cụ Ruby được sử dụng để kiểm thử do người tiêu dùng điều khiển. Nó có khả năng ghi lại các tương tác giữa máy khách và máy chủ để tạo ra các kỳ vọng. Điều này làm cho việc viết các hợp đồng do người tiêu dùng điều khiển cho các dịch vụ hiện có khá dễ dàng. Với Pacto, một khi được tạo ra, những kỳ vọng này ít nhiều là tĩnh, trong khi với Pact, bạn tạo lại các kỳ vọng trong người tiêu dùng với mỗi bản dựng. Thực tế là bạn có thể định nghĩa các kỳ vọng cho các khả năng mà nhà sản xuất có thể chưa có cũng phù hợp hơn với một quy trình làm việc nơi dịch vụ sản xuất vẫn đang được (hoặc chưa được) phát triển.

*(Phần tiếp theo và cuối cùng của chương này sẽ thảo luận về "It’s About Conversations", "So Should You Use End-to-End Tests?", "Testing After Production", và các kỹ thuật liên quan.)*

Chắc chắn rồi! Chúng ta sẽ hoàn thành Chương 7, xem xét các khía cạnh cuối cùng của chiến lược kiểm thử, bao gồm tầm quan trọng của giao tiếp, khi nào nên sử dụng kiểm thử đầu cuối, và việc kiểm thử sau khi đã triển khai.

---

##### **Đó là về những cuộc Trò chuyện (It’s About Conversations)**

Trong phương pháp agile, các `story` thường được coi là một nơi giữ chỗ cho một cuộc trò chuyện. CDC cũng giống như vậy. Chúng trở thành sự mã hóa của một loạt các cuộc thảo luận về một API dịch vụ nên trông như thế nào, và khi chúng bị phá vỡ, chúng trở thành một điểm kích hoạt để có các cuộc trò chuyện về cách API đó nên phát triển.

Điều quan trọng là phải hiểu rằng CDC đòi hỏi sự giao tiếp tốt và sự tin tưởng giữa người tiêu dùng và dịch vụ sản xuất. Nếu cả hai bên đều ở trong cùng một đội (hoặc cùng một người!), thì điều này không nên khó khăn. Tuy nhiên, nếu bạn đang tiêu thụ một dịch vụ được cung cấp bởi một bên thứ ba, bạn có thể không có tần suất giao tiếp, hoặc sự tin tưởng, để làm cho CDC hoạt động. Trong những tình huống này, bạn có thể phải chấp nhận các bài kiểm thử tích hợp có phạm vi lớn hơn chỉ xung quanh thành phần không đáng tin cậy. Hoặc, nếu bạn đang tạo ra một API cho hàng ngàn người tiêu dùng tiềm năng, chẳng hạn như với một API dịch vụ web công khai, bạn có thể phải đóng vai trò của chính người tiêu dùng (hoặc có lẽ làm việc với một tập hợp con của người tiêu dùng của bạn) trong việc định nghĩa những bài kiểm thử này. Việc làm hỏng một số lượng lớn người tiêu dùng bên ngoài là một ý tưởng khá tồi tệ, vì vậy nếu có bất cứ điều gì, tầm quan trọng của CDC lại càng tăng lên!

#### **Vậy bạn có nên sử dụng Kiểm thử Đầu cuối không? (So Should You Use End-to-End Tests?)**

Như đã nêu chi tiết trước đó trong chương, các bài kiểm thử đầu cuối có một số lượng lớn các nhược điểm tăng lên đáng kể khi bạn thêm nhiều bộ phận chuyển động vào kiểm thử. Từ việc nói chuyện với những người đã triển khai microservices ở quy mô lớn trong một thời gian, tôi đã học được rằng hầu hết họ theo thời gian đã loại bỏ hoàn toàn nhu cầu về các bài kiểm thử đầu cuối để thay vào đó là các công cụ như CDC và giám sát được cải thiện. Nhưng họ không nhất thiết phải vứt bỏ những bài kiểm thử đó. Họ cuối cùng sử dụng nhiều trong số những bài kiểm thử hành trình đầu cuối đó để giám sát hệ thống sản xuất bằng cách sử dụng một kỹ thuật được gọi là **giám sát ngữ nghĩa (semantic monitoring)**, mà chúng ta sẽ thảo luận nhiều hơn trong **Chương 8**.

Bạn có thể xem việc chạy các bài kiểm thử đầu cuối trước khi triển khai sản xuất như là **bánh xe tập đi**. Trong khi bạn đang học cách CDC hoạt động, và cải thiện các kỹ thuật giám sát và triển khai sản xuất của mình, các bài kiểm thử đầu cuối này có thể tạo thành một mạng lưới an toàn hữu ích, nơi bạn đang đánh đổi thời gian chu kỳ để giảm rủi ro. Nhưng khi bạn cải thiện những lĩnh vực khác, bạn có thể bắt đầu giảm sự phụ thuộc của mình vào các bài kiểm thử đầu cuối đến mức chúng không còn cần thiết nữa.

Tương tự như vậy, bạn có thể làm việc trong một môi trường nơi khẩu vị học hỏi trong sản xuất thấp, và mọi người thà làm việc chăm chỉ nhất có thể để loại bỏ bất kỳ khiếm khuyết nào trước khi sản xuất, ngay cả khi điều đó có nghĩa là phần mềm mất nhiều thời gian hơn để được phát hành. Miễn là bạn hiểu rằng bạn không thể chắc chắn rằng bạn đã loại bỏ tất cả các nguồn khiếm khuyết, và bạn vẫn sẽ cần phải có hệ thống giám sát và khắc phục sự cố hiệu quả tại chỗ trong sản xuất, đây có thể là một quyết định hợp lý.

Rõ ràng bạn sẽ có một sự hiểu biết tốt hơn về hồ sơ rủi ro của tổ chức của riêng bạn hơn tôi, nhưng tôi sẽ thách thức bạn suy nghĩ lâu dài và kỹ lưỡng về việc bạn thực sự cần bao nhiêu bài kiểm thử đầu cuối.

#### **Kiểm thử sau Sản xuất (Testing After Production)**

Hầu hết việc kiểm thử được thực hiện trước khi hệ thống được đưa vào sản xuất. Với các bài kiểm thử của chúng ta, chúng ta đang định nghĩa một loạt các mô hình mà chúng ta hy vọng sẽ chứng minh liệu hệ thống của chúng ta có hoạt động và hành xử như chúng ta mong muốn hay không, cả về mặt chức năng và phi chức năng. Nhưng nếu các mô hình của chúng ta không hoàn hảo, thì chúng ta sẽ gặp phải các vấn đề khi hệ thống của chúng ta được sử dụng thực sự. Các lỗi lọt vào sản xuất, các chế độ lỗi mới được phát hiện, và người dùng của chúng ta sử dụng hệ thống theo những cách mà chúng ta không bao giờ có thể mong đợi.

Một phản ứng đối với điều này thường là định nghĩa ngày càng nhiều bài kiểm thử, và tinh chỉnh các mô hình của chúng ta, để bắt được nhiều vấn đề hơn sớm hơn và giảm số lượng các vấn đề chúng ta gặp phải với hệ thống sản xuất đang chạy của mình. Tuy nhiên, tại một thời điểm nhất định, chúng ta phải chấp nhận rằng chúng ta đạt đến lợi nhuận giảm dần với cách tiếp cận này. Với việc kiểm thử trước khi triển khai, chúng ta không thể giảm cơ hội thất bại xuống con số không.

##### **Tách biệt Triển khai khỏi Phát hành (Separating Deployment from Release)**

Một cách mà chúng ta có thể bắt được nhiều vấn đề hơn trước khi chúng xảy ra là mở rộng nơi chúng ta chạy các bài kiểm thử của mình ra ngoài các bước tiền triển khai truyền thống. Thay vào đó, nếu chúng ta có thể triển khai phần mềm của mình, và kiểm thử nó **tại chỗ (in situ)** trước khi hướng tải sản xuất vào nó, chúng ta có thể phát hiện các vấn đề cụ thể cho một môi trường nhất định. Một ví dụ phổ biến của điều này là **bộ kiểm thử khói (smoke test suite)**, một tập hợp các bài kiểm thử được thiết kế để chạy chống lại phần mềm mới được triển khai để xác nhận rằng việc triển khai đã hoạt động. Các bài kiểm thử này giúp bạn phát hiện bất kỳ vấn đề môi trường cục bộ nào. Nếu bạn đang sử dụng một lệnh dòng lệnh duy nhất để triển khai bất kỳ microservice nào (và bạn nên làm vậy), lệnh này nên chạy các bài kiểm thử khói một cách tự động.

Một ví dụ khác của điều này là cái được gọi là **triển khai blue/green**. Với blue/green, chúng ta có hai bản sao của phần mềm được triển khai cùng một lúc, nhưng chỉ có một phiên bản của nó đang nhận các yêu cầu thực sự.

Hãy xem xét một ví dụ đơn giản, được thấy trong Hình 7-12. Trong sản xuất, chúng ta có v123 của dịch vụ khách hàng đang hoạt động. Chúng ta muốn triển khai một phiên bản mới, v456. Chúng ta triển khai nó song song với v123, nhưng không hướng bất kỳ lưu lượng truy cập nào đến nó. Thay vào đó, chúng ta thực hiện một số kiểm thử tại chỗ chống lại phiên bản mới được triển khai. Một khi các bài kiểm thử đã hoạt động, chúng ta hướng tải sản xuất đến phiên bản v456 mới của dịch vụ khách hàng. Thường thì người ta giữ phiên bản cũ một thời gian ngắn, cho phép quay lui nhanh nếu bạn phát hiện bất kỳ lỗi nào.

![alt text](<images/Screenshot from 2025-09-29 13-11-18.png>)

**Hình 7-12.** *Sử dụng các triển khai blue/green để tách biệt việc triển khai khỏi phát hành*

Việc triển khai blue/green đòi hỏi một vài điều.
*   **Thứ nhất**, bạn cần có khả năng hướng lưu lượng sản xuất đến các máy chủ (hoặc các tập hợp máy chủ) khác nhau. Bạn có thể làm điều này bằng cách thay đổi các bản ghi DNS, hoặc cập nhật cấu hình cân bằng tải.
*   Bạn cũng cần có khả năng cấp phát đủ máy chủ để có cả hai phiên bản của microservice chạy cùng một lúc. Nếu bạn đang sử dụng một nhà cung cấp đám mây linh hoạt, điều này có thể đơn giản.

Việc sử dụng các triển khai blue/green cho phép bạn giảm rủi ro triển khai, cũng như cho bạn cơ hội hoàn tác nếu bạn gặp sự cố. Nếu bạn làm tốt điều này, toàn bộ quy trình có thể được tự động hóa hoàn toàn, với việc triển khai toàn bộ hoặc hoàn tác xảy ra mà không có bất kỳ sự can thiệp nào của con người.

Hoàn toàn ngoài lợi ích của việc cho phép chúng ta kiểm thử các dịch vụ của mình tại chỗ trước khi gửi lưu lượng sản xuất cho chúng, bằng cách giữ phiên bản cũ chạy trong khi chúng ta thực hiện việc phát hành, chúng ta giảm đáng kể thời gian ngừng hoạt động liên quan đến việc phát hành phần mềm của mình. Tùy thuộc vào cơ chế được sử dụng để thực hiện việc chuyển hướng lưu lượng truy cập, việc chuyển đổi giữa các phiên bản có thể hoàn toàn vô hình đối với khách hàng, mang lại cho chúng ta các đợt triển khai không có thời gian ngừng hoạt động.

Có một kỹ thuật khác đáng để thảo luận ngắn gọn ở đây, đôi khi bị nhầm lẫn với các triển khai blue/green, vì nó có thể sử dụng một số triển khai kỹ thuật tương tự. Nó được biết đến với tên gọi **phát hành canary (canary releasing)**.

##### **Phát hành Canary (Canary Releasing)**

Với phát hành canary, chúng ta đang xác minh phần mềm mới được triển khai của mình bằng cách hướng một lượng lưu lượng sản xuất vào hệ thống để xem liệu nó có hoạt động như mong đợi hay không. "Hoạt động như mong đợi" có thể bao gồm một số thứ, cả chức năng và phi chức năng. Ví dụ, chúng ta có thể kiểm tra xem một dịch vụ mới được triển khai có phản hồi các yêu cầu trong vòng 500ms hay không, hoặc liệu chúng ta có thấy cùng một tỷ lệ lỗi tương ứng từ dịch vụ mới và cũ hay không. Nhưng bạn có thể đi sâu hơn thế. Hãy tưởng tượng chúng ta đã phát hành một phiên bản mới của dịch vụ đề xuất. Chúng ta có thể chạy cả hai song song nhưng xem liệu các đề xuất được tạo ra bởi phiên bản mới của dịch vụ có dẫn đến nhiều doanh số mong đợi như nhau hay không, đảm bảo rằng chúng ta không phát hành một thuật toán dưới tối ưu.

Nếu bản phát hành mới là tồi tệ, bạn có thể hoàn tác nhanh chóng. Nếu nó tốt, bạn có thể đẩy lượng lưu lượng truy cập ngày càng tăng qua phiên bản mới. Phát hành canary khác với blue/green ở chỗ bạn có thể mong đợi các phiên bản cùng tồn tại lâu hơn, và bạn thường sẽ thay đổi lượng lưu lượng truy cập.

Netflix sử dụng cách tiếp cận này một cách rộng rãi. Trước khi phát hành, các phiên bản dịch vụ mới được triển khai song song với một cụm cơ sở đại diện cho cùng một phiên bản như sản xuất. Netflix sau đó chạy một tập hợp con của tải sản xuất trong vài giờ chống lại cả phiên bản mới và cơ sở, chấm điểm cả hai. Nếu canary vượt qua, công ty sau đó tiến hành triển khai toàn bộ vào sản xuất.

Khi xem xét phát hành canary, bạn cần quyết định xem bạn sẽ chuyển hướng một phần yêu cầu sản xuất đến canary hay chỉ sao chép tải sản xuất. Một số đội có thể **phản chiếu (shadow)** lưu lượng sản xuất và hướng nó đến canary của họ. Bằng cách này, các phiên bản sản xuất và canary hiện có có thể thấy chính xác các yêu cầu giống nhau, nhưng chỉ có kết quả của các yêu cầu sản xuất được nhìn thấy từ bên ngoài. Điều này cho phép bạn thực hiện một so sánh song song trong khi loại bỏ khả năng một lỗi trong canary có thể được nhìn thấy bởi một yêu cầu của khách hàng. Tuy nhiên, công việc phản chiếu lưu lượng sản xuất có thể phức tạp, đặc biệt nếu các sự kiện/yêu cầu đang được phát lại không phải là **idempotent**.

Phát hành canary là một kỹ thuật mạnh mẽ, và có thể giúp bạn xác minh các phiên bản mới của phần mềm của mình với lưu lượng truy cập thực, đồng thời cung cấp cho bạn các công cụ để quản lý rủi ro của việc đẩy ra một bản phát hành tồi. Tuy nhiên, nó đòi hỏi một thiết lập phức tạp hơn, so với triển khai blue/green, và một chút suy nghĩ nhiều hơn. Bạn có thể mong đợi các phiên bản khác nhau của các dịch vụ của mình cùng tồn tại lâu hơn so với với blue/green, vì vậy bạn có thể đang chiếm dụng nhiều phần cứng hơn trong một thời gian dài hơn. Bạn cũng sẽ cần định tuyến lưu lượng truy cập phức tạp hơn, vì bạn có thể muốn tăng hoặc giảm tỷ lệ phần trăm của lưu lượng truy cập để có được sự tự tin hơn rằng bản phát hành của bạn hoạt động. Nếu bạn đã xử lý các triển khai blue/green, bạn có thể đã có một số khối xây dựng rồi.

##### **Thời gian Trung bình để Sửa chữa so với Thời gian Trung bình giữa các Lỗi? (Mean Time to Repair Over Mean Time Between Failures?)**

Vì vậy, bằng cách xem xét các kỹ thuật như triển khai blue/green hoặc phát hành canary, chúng ta tìm ra một cách để kiểm thử gần hơn (hoặc thậm chí trong) sản xuất, và chúng ta cũng xây dựng các công cụ để giúp chúng ta quản lý một lỗi nếu nó xảy ra. Việc sử dụng các phương pháp này là một sự thừa nhận ngầm rằng chúng ta không thể phát hiện và bắt tất cả các vấn đề trước khi chúng ta thực sự phát hành phần mềm của mình.

Đôi khi việc dành cùng một nỗ lực vào việc trở nên tốt hơn trong việc khắc phục một bản phát hành có thể mang lại lợi ích đáng kể hơn so với việc thêm nhiều bài kiểm thử chức năng tự động hơn. Trong thế giới vận hành web, điều này thường được gọi là sự đánh đổi giữa việc tối ưu hóa cho **thời gian trung bình giữa các lỗi (mean time between failures - MTBF)** và **thời gian trung bình để sửa chữa (mean time to repair - MTTR)**.

Các kỹ thuật để giảm thời gian phục hồi có thể đơn giản như việc hoàn tác rất nhanh kết hợp với giám sát tốt (mà chúng ta sẽ thảo luận trong **Chương 8**), như các triển khai blue/green. Nếu chúng ta có thể phát hiện một vấn đề trong sản xuất sớm, và hoàn tác sớm, chúng ta sẽ giảm tác động đến khách hàng của mình. Chúng ta cũng có thể sử dụng các kỹ thuật như triển khai blue/green, nơi chúng ta triển khai một phiên bản mới của phần mềm và kiểm thử nó tại chỗ trước khi hướng người dùng của chúng ta đến phiên bản mới.

Đối với các tổ chức khác nhau, sự đánh đổi giữa MTBF và MTTR sẽ khác nhau, và phần lớn điều này nằm ở việc hiểu được tác động thực sự của sự thất bại trong một môi trường sản xuất. Tuy nhiên, hầu hết các tổ chức mà tôi thấy dành thời gian tạo ra các bộ kiểm thử chức năng thường dành rất ít hoặc không có nỗ lực nào cho việc giám sát tốt hơn hoặc phục hồi sau thất bại. Vì vậy, trong khi họ có thể giảm số lượng khiếm khuyết xảy ra ngay từ đầu, họ không thể loại bỏ tất cả chúng, và không được chuẩn bị để đối phó với chúng nếu chúng xuất hiện trong sản xuất.

Các sự đánh đổi khác ngoài MTBF và MTTR tồn tại. Ví dụ, nếu bạn đang cố gắng tìm ra liệu có ai sẽ thực sự sử dụng phần mềm của bạn hay không, có thể hợp lý hơn nhiều nếu đưa một cái gì đó ra ngoài ngay bây giờ, để chứng minh ý tưởng hoặc mô hình kinh doanh trước khi xây dựng phần mềm mạnh mẽ. Trong một môi trường mà đây là trường hợp, việc kiểm thử có thể là quá mức cần thiết, vì tác động của việc không biết liệu ý tưởng của bạn có hoạt động hay không cao hơn nhiều so với việc có một khiếm khuyết trong sản xuất. Trong những tình huống này, có thể khá hợp lý để tránh kiểm thử trước khi sản xuất hoàn toàn.

##### **Kiểm thử Chức năng Chéo (Cross-Functional Testing)**

Phần lớn của chương này đã tập trung vào việc kiểm thử các phần chức năng cụ thể, và điều này khác nhau như thế nào khi bạn đang kiểm thử một hệ thống dựa trên microservice. Tuy nhiên, có một loại kiểm thử khác quan trọng cần thảo luận. **Yêu cầu phi chức năng (Nonfunctional requirements)** là một thuật ngữ bao trùm được sử dụng để mô tả những đặc điểm mà hệ thống của bạn thể hiện không thể được triển khai đơn giản như một tính năng thông thường. Chúng bao gồm các khía cạnh như độ trễ chấp nhận được của một trang web, số lượng người dùng mà một hệ thống nên hỗ trợ, giao diện người dùng của bạn nên dễ tiếp cận như thế nào đối với những người khuyết tật, hoặc dữ liệu khách hàng của bạn nên được bảo mật như thế nào.

Thuật ngữ *phi chức năng* chưa bao giờ thực sự phù hợp với tôi. Một số thứ được đề cập bởi thuật ngữ này dường như rất chức năng trong bản chất! Một trong những đồng nghiệp của tôi, Sarah Taraporewalla, đã đặt ra cụm từ **yêu cầu chức năng chéo (cross-functional requirements - CFR)** thay vào đó, mà tôi rất thích. Nó nói nhiều hơn về thực tế là những hành vi hệ thống này thực sự chỉ xuất hiện như là kết quả của rất nhiều công việc cắt ngang.

Nhiều, nếu không muốn nói là hầu hết, các CFR thực sự chỉ có thể được đáp ứng trong sản xuất. Điều đó nói rằng, chúng ta có thể định nghĩa các chiến lược kiểm thử để giúp chúng ta xem liệu chúng ta ít nhất có đang đi đúng hướng để đáp ứng các mục tiêu này hay không. Các loại kiểm thử này rơi vào góc phần tư **Kiểm thử Thuộc tính (Property Testing)**. Một ví dụ tuyệt vời của điều này là kiểm thử hiệu suất, mà chúng ta sẽ thảo luận sâu hơn ngay sau đây.

Đối với một số CFR, bạn có thể muốn theo dõi chúng ở cấp độ dịch vụ riêng lẻ. Ví dụ, bạn có thể quyết định rằng độ bền của dịch vụ bạn yêu cầu từ dịch vụ thanh toán của mình cao hơn đáng kể, nhưng bạn hài lòng với nhiều thời gian ngừng hoạt động hơn cho dịch vụ đề xuất âm nhạc của mình, biết rằng hoạt động kinh doanh cốt lõi của bạn có thể tồn tại nếu bạn không thể đề xuất các nghệ sĩ tương tự như Metallica trong 10 phút hoặc lâu hơn. Những sự đánh đổi này cuối cùng sẽ có tác động lớn đến cách bạn thiết kế và phát triển hệ thống của mình, và một lần nữa, bản chất chi tiết của một hệ thống dựa trên microservice cung cấp cho bạn nhiều cơ hội hơn để thực hiện những sự đánh đổi này.

Các bài kiểm thử xung quanh các CFR cũng nên tuân theo kim tự tháp. Một số bài kiểm thử sẽ phải là đầu cuối, như các bài kiểm thử tải, nhưng những bài khác thì không. Ví dụ, một khi bạn đã tìm thấy một nút thắt cổ chai về hiệu suất trong một bài kiểm thử tải đầu cuối, hãy viết một bài kiểm thử có phạm vi nhỏ hơn để giúp bạn bắt vấn đề trong tương lai. Các CFR khác phù hợp với các bài kiểm thử nhanh hơn khá dễ dàng. Tôi nhớ đã làm việc trên một dự án nơi chúng tôi đã khăng khăng đảm bảo rằng đánh dấu `HTML` của chúng tôi đang sử dụng các tính năng trợ năng phù hợp để giúp những người khuyết tật sử dụng trang web của chúng tôi. Việc kiểm tra đánh dấu được tạo ra để đảm bảo rằng các điều khiển phù hợp có ở đó có thể được thực hiện rất nhanh chóng mà không cần bất kỳ chuyến đi khứ hồi mạng nào.

Tất cả quá thường xuyên, những cân nhắc về các CFR đến quá muộn. Tôi thực sự đề nghị xem xét các CFR của bạn càng sớm càng tốt, và xem xét chúng thường xuyên.

##### **Kiểm thử Hiệu suất (Performance Tests)**

Các bài kiểm thử hiệu suất đáng được nêu ra một cách rõ ràng như một cách để đảm bảo rằng một số yêu cầu chức năng chéo của chúng ta có thể được đáp ứng. Khi phân tách các hệ thống thành các microservice nhỏ hơn, chúng ta tăng số lượng các cuộc gọi sẽ được thực hiện qua các ranh giới mạng. Nơi mà trước đây một hoạt động có thể đã liên quan đến một cuộc gọi cơ sở dữ liệu, bây giờ nó có thể liên quan đến ba hoặc bốn cuộc gọi qua các ranh giới mạng đến các dịch vụ khác, với một số lượng tương ứng các cuộc gọi cơ sở dữ liệu. Tất cả điều này có thể làm giảm tốc độ hoạt động của hệ thống của chúng ta. Việc theo dõi các nguồn độ trễ là đặc biệt quan trọng. Khi bạn có một chuỗi cuộc gọi gồm nhiều cuộc gọi đồng bộ, nếu bất kỳ phần nào của chuỗi bắt đầu hoạt động chậm, mọi thứ đều bị ảnh hưởng, có khả năng dẫn đến một tác động đáng kể.

Điều này làm cho việc có một cách nào đó để kiểm thử hiệu suất các ứng dụng của bạn trở nên quan trọng hơn so với khi có một hệ thống `monolithic` hơn. Thường thì lý do loại kiểm thử này bị trì hoãn là vì ban đầu không có đủ hệ thống ở đó để kiểm thử. Tôi hiểu vấn đề này, nhưng tất cả quá thường xuyên nó dẫn đến việc đá lon xuống đường, với việc kiểm thử hiệu suất thường chỉ được thực hiện ngay trước khi bạn đi vào hoạt động lần đầu tiên, nếu có! Đừng rơi vào cái bẫy này.

Cũng như với các bài kiểm thử chức năng, bạn có thể muốn có một sự kết hợp. Bạn có thể quyết định rằng bạn muốn các bài kiểm thử hiệu suất cô lập các dịch vụ riêng lẻ, nhưng hãy bắt đầu với các bài kiểm thử kiểm tra các hành trình cốt lõi trong hệ thống của bạn. Bạn có thể có thể lấy các bài kiểm thử hành trình đầu cuối và chỉ cần chạy chúng với khối lượng lớn.

Để tạo ra các kết quả đáng giá, bạn thường sẽ cần phải chạy các kịch bản nhất định với số lượng khách hàng mô phỏng tăng dần. Điều này cho phép bạn xem độ trễ của các cuộc gọi thay đổi như thế nào với tải tăng lên. Điều này có nghĩa là các bài kiểm thử hiệu suất có thể mất một thời gian để chạy. Ngoài ra, bạn sẽ muốn hệ thống khớp với sản xuất càng sát càng tốt, để đảm bảo rằng các kết quả bạn thấy sẽ là chỉ báo về hiệu suất bạn có thể mong đợi trên các hệ thống sản xuất. Điều này có thể có nghĩa là bạn sẽ cần phải có được một khối lượng dữ liệu giống sản xuất hơn, và có thể cần nhiều máy hơn để khớp với cơ sở hạ tầng—những nhiệm vụ có thể là một thách thức. Ngay cả khi bạn gặp khó khăn trong việc làm cho môi trường hiệu suất thực sự giống sản xuất, các bài kiểm thử vẫn có thể có giá trị trong việc theo dõi các nút thắt cổ chai. Chỉ cần lưu ý rằng bạn có thể nhận được các kết quả âm tính giả, hoặc thậm chí tệ hơn, dương tính giả.

Do thời gian cần thiết để chạy các bài kiểm thử hiệu suất, việc chạy chúng trên mỗi lần `check-in` không phải lúc nào cũng khả thi. Một thực hành phổ biến là chạy một tập hợp con mỗi ngày, và một tập hợp lớn hơn mỗi tuần. Bất kể bạn chọn cách tiếp cận nào, hãy chắc chắn rằng bạn chạy chúng thường xuyên nhất có thể. Càng lâu bạn không chạy các bài kiểm thử hiệu suất, càng khó để truy ra thủ phạm. Các vấn đề về hiệu suất đặc biệt khó giải quyết, vì vậy nếu bạn có thể giảm số lượng các `commit` bạn cần xem xét để thấy một vấn đề mới được giới thiệu, cuộc sống của bạn sẽ dễ dàng hơn nhiều.

Và hãy chắc chắn rằng bạn cũng xem xét các kết quả! Tôi đã rất ngạc nhiên bởi số lượng các đội tôi đã gặp đã dành rất nhiều công việc để triển khai các bài kiểm thử và chạy chúng, và không bao giờ kiểm tra các con số. Thường thì điều này là do mọi người không biết một kết quả tốt trông như thế nào. Bạn thực sự cần phải có các mục tiêu. Bằng cách này, bạn có thể làm cho bản dựng chuyển sang màu đỏ hoặc xanh lá cây dựa trên các kết quả, với một bản dựng màu đỏ (thất bại) là một lời kêu gọi hành động rõ ràng.

Việc kiểm thử hiệu suất cần được thực hiện cùng với việc giám sát hiệu suất hệ thống thực (mà chúng ta sẽ thảo luận nhiều hơn trong **Chương 8**), và lý tưởng nhất là nên sử dụng các công cụ tương tự trong môi trường kiểm thử hiệu suất của bạn để trực quan hóa hành vi hệ thống như những công cụ bạn sử dụng trong sản xuất. Điều này có thể làm cho việc so sánh giống như với giống trở nên dễ dàng hơn nhiều.

---
#### **Tóm tắt (Summary)**

Tổng hợp tất cả lại, những gì tôi đã phác thảo ở đây là một cách tiếp cận toàn diện để kiểm thử mà hy vọng sẽ cung cấp cho bạn một số hướng dẫn chung về cách tiến hành khi kiểm thử các hệ thống của riêng bạn. Để nhắc lại những điều cơ bản:

*   **Tối ưu hóa cho phản hồi nhanh, và tách các loại kiểm thử một cách tương ứng.**
*   **Tránh nhu cầu về các bài kiểm thử đầu cuối bất cứ khi nào có thể bằng cách sử dụng các hợp đồng do người tiêu dùng điều khiển.**
*   **Sử dụng các hợp đồng do người tiêu dùng điều khiển để cung cấp các điểm tập trung cho các cuộc trò chuyện giữa các đội.**
*   **Cố gắng hiểu sự đánh đổi giữa việc nỗ lực nhiều hơn vào việc kiểm thử và phát hiện các vấn đề nhanh hơn trong sản xuất (tối ưu hóa cho MTBF so với MTTR).**

Nếu bạn quan tâm đến việc đọc thêm về kiểm thử, tôi khuyên bạn nên đọc cuốn *Agile Testing* của Lisa Crispin và Janet Gregory (Addison-Wesley), trong đó, trong số những thứ khác, đề cập đến việc sử dụng góc phần tư kiểm thử một cách chi tiết hơn.

Chương này tập trung chủ yếu vào việc đảm bảo mã của chúng ta hoạt động trước khi nó được đưa vào sản xuất, nhưng chúng ta cũng cần biết cách đảm bảo mã của chúng ta hoạt động một khi nó đã được triển khai. Trong chương tiếp theo, chúng ta sẽ xem xét cách giám sát các hệ thống dựa trên microservice của mình.

Tuyệt vời! Chúng ta sẽ tiếp tục với Chương 8: "Giám sát (Monitoring)". Chương này sẽ khám phá những thách thức và giải pháp để theo dõi sức khỏe của một hệ thống microservice phức tạp.

---

### **8. Giám sát (Monitoring)**

Như tôi hy vọng đã chỉ ra cho đến nay, việc chia nhỏ hệ thống của chúng ta thành các microservice nhỏ hơn, chi tiết hơn mang lại nhiều lợi ích. Tuy nhiên, nó cũng thêm sự phức tạp khi nói đến việc giám sát hệ thống trong sản xuất. Trong chương này, chúng ta sẽ xem xét những thách thức liên quan đến việc giám sát và xác định các vấn đề trong các hệ thống chi tiết của chúng ta, và tôi sẽ phác thảo một số điều bạn có thể làm để vừa có được chiếc bánh của mình vừa ăn được nó!

Hãy hình dung cảnh tượng. Đó là một buổi chiều thứ Sáu yên tĩnh, và đội ngũ đang mong chờ được nghỉ sớm để đến quán rượu như một cách để bắt đầu một kỳ nghỉ cuối tuần xa công việc. Rồi đột nhiên các email đến. Trang web đang hoạt động sai! Twitter đang bùng cháy với những thất bại của công ty bạn, sếp của bạn đang la mắng bạn, và viễn cảnh về một kỳ nghỉ cuối tuần yên tĩnh tan biến.

Điều đầu tiên bạn cần biết là gì? Cái quái gì đã xảy ra vậy?

Trong thế giới của ứng dụng `monolithic`, ít nhất chúng ta có một nơi rất rõ ràng để bắt đầu các cuộc điều tra của mình. Trang web chậm? Đó là `monolith`. Trang web báo lỗi lạ? Đó là `monolith`. CPU ở mức 100%? `Monolith`. Mùi khét? Chà, bạn hiểu ý rồi đấy. Việc có một điểm lỗi duy nhất cũng làm cho việc điều tra lỗi trở nên đơn giản hơn!

Bây giờ hãy nghĩ về hệ thống dựa trên microservice của riêng chúng ta. Các khả năng chúng ta cung cấp cho người dùng của mình được phục vụ từ nhiều dịch vụ nhỏ, một số trong đó giao tiếp với nhiều dịch vụ hơn nữa để hoàn thành nhiệm vụ của chúng. Có rất nhiều lợi thế đối với một cách tiếp cận như vậy (điều này tốt, nếu không cuốn sách này sẽ là một sự lãng phí thời gian), nhưng trong thế giới giám sát, chúng ta có một vấn đề phức tạp hơn trong tay.

Bây giờ chúng ta có nhiều máy chủ để giám sát, nhiều tệp nhật ký để sàng lọc, và nhiều nơi mà độ trễ mạng có thể gây ra sự cố. Vậy chúng ta tiếp cận vấn đề này như thế nào? Chúng ta cần phải hiểu được những gì có thể là một mớ hỗn độn, rối rắm—điều cuối cùng mà bất kỳ ai trong chúng ta muốn đối phó vào một chiều thứ Sáu (hoặc bất cứ lúc nào, thực ra!).

Câu trả lời ở đây khá đơn giản: **giám sát những thứ nhỏ, và sử dụng sự tổng hợp để xem bức tranh lớn hơn**. Để xem cách làm, chúng ta sẽ bắt đầu với hệ thống đơn giản nhất có thể: một nút duy nhất.

#### **Một Dịch vụ, Một Máy chủ (Single Service, Single Server)**

Hình 8-1 trình bày một thiết lập rất đơn giản: một máy chủ, chạy một dịch vụ. Bây giờ chúng ta cần giám sát nó để biết khi nào có sự cố xảy ra, để chúng ta có thể khắc phục nó. Vậy chúng ta nên tìm kiếm điều gì?

![alt text](<images/Screenshot from 2025-09-29 13-11-24.png>)

**Hình 8-1.** *Một dịch vụ duy nhất trên một máy chủ duy nhất*

*   **Thứ nhất**, chúng ta sẽ muốn giám sát chính máy chủ. CPU, bộ nhớ—tất cả những thứ này đều hữu ích. Chúng ta sẽ muốn biết chúng nên ở mức nào khi mọi thứ đang khỏe mạnh, để chúng ta có thể cảnh báo khi chúng vượt ra ngoài giới hạn. Nếu chúng ta muốn chạy phần mềm giám sát của riêng mình, chúng ta có thể sử dụng một cái gì đó như Nagios để làm điều đó, hoặc sử dụng một dịch vụ được lưu trữ như New Relic.
*   **Tiếp theo**, chúng ta sẽ muốn có quyền truy cập vào các nhật ký (`logs`) từ chính máy chủ. Nếu một người dùng báo cáo một lỗi, những nhật ký này sẽ ghi lại nó và hy vọng cho chúng ta biết khi nào và ở đâu lỗi xảy ra. Tại thời điểm này, với một máy chủ duy nhất của chúng ta, chúng ta có thể chỉ cần đăng nhập vào máy chủ và sử dụng các công cụ dòng lệnh để quét nhật ký. Chúng ta thậm chí có thể trở nên tiên tiến và sử dụng `logrotate` để di chuyển các nhật ký cũ đi và tránh chúng chiếm hết dung lượng đĩa của chúng ta.
*   **Cuối cùng**, chúng ta có thể muốn giám sát chính ứng dụng. Ở mức tối thiểu, việc giám sát thời gian phản hồi của dịch vụ là một ý tưởng tốt. Bạn có thể sẽ có thể làm điều này bằng cách xem xét các nhật ký đến từ một máy chủ web đứng trước dịch vụ của bạn, hoặc có lẽ từ chính dịch vụ đó. Nếu chúng ta trở nên rất tiên tiến, chúng ta có thể muốn theo dõi số lượng các lỗi chúng ta đang báo cáo.

Thời gian trôi qua, tải trọng tăng lên, và chúng ta thấy mình cần phải mở rộng quy mô…

#### **Một Dịch vụ, Nhiều Máy chủ (Single Service, Multiple Servers)**

Bây giờ chúng ta có nhiều bản sao của dịch vụ chạy trên các máy chủ riêng biệt, như được hiển thị trong Hình 8-2, với các yêu cầu đến các phiên bản dịch vụ khác nhau được phân phối thông qua một bộ cân bằng tải. Mọi thứ bắt đầu trở nên phức tạp hơn một chút. Chúng ta vẫn muốn giám sát tất cả những thứ giống như trước đây, nhưng cần phải làm như vậy theo cách mà chúng ta có thể cô lập vấn đề.

![alt text](<images/Screenshot from 2025-09-29 13-11-32.png>)

**Hình 8-2.** *Một dịch vụ duy nhất được phân phối trên nhiều máy chủ*

Khi CPU cao, đó có phải là một vấn đề chúng ta đang thấy trên tất cả các máy chủ, điều này sẽ chỉ ra một vấn đề với chính dịch vụ, hay nó bị cô lập ở một máy chủ duy nhất, ngụ ý rằng chính máy chủ đó có vấn đề—có lẽ là một tiến trình HĐH lừa đảo?

Vì vậy, tại thời điểm này, chúng ta vẫn muốn theo dõi các chỉ số cấp máy chủ, và cảnh báo về chúng. Nhưng bây giờ chúng ta muốn xem chúng trên tất cả các máy chủ, cũng như các máy chủ riêng lẻ. Nói cách khác, chúng ta muốn **tổng hợp (aggregate)** chúng lên, và vẫn có thể **đi sâu vào chi tiết (drill down)**. Nagios cho phép chúng ta nhóm các máy chủ của mình như thế này—cho đến nay thì tốt. Một cách tiếp cận tương tự có lẽ sẽ đủ cho ứng dụng của chúng ta.

Sau đó, chúng ta có các nhật ký của mình. Với dịch vụ của chúng ta chạy trên nhiều hơn một máy chủ, chúng ta có lẽ sẽ cảm thấy mệt mỏi khi phải đăng nhập vào từng hộp để xem nó. Tuy nhiên, với chỉ một vài máy chủ, chúng ta có thể sử dụng các công cụ như `ssh-multiplexers`, cho phép chúng ta chạy các lệnh giống nhau trên nhiều máy chủ. Một màn hình lớn và một lệnh `grep "Error" app.log` sau đó, và chúng ta có thể tìm ra thủ phạm của mình.

Đối với các tác vụ như theo dõi thời gian phản hồi, chúng ta có thể nhận được một số sự tổng hợp miễn phí bằng cách theo dõi tại chính bộ cân bằng tải. Nhưng tất nhiên, chúng ta cần phải theo dõi cả bộ cân bằng tải; nếu nó hoạt động sai, chúng ta có một vấn đề. Tại thời điểm này, chúng ta cũng có lẽ quan tâm nhiều hơn về một dịch vụ khỏe mạnh trông như thế nào, vì chúng ta sẽ cấu hình bộ cân bằng tải của mình để loại bỏ các nút không lành mạnh khỏi ứng dụng của mình. Hy vọng đến lúc này chúng ta đã có ít nhất một số ý tưởng về điều đó…

#### **Nhiều Dịch vụ, Nhiều Máy chủ (Multiple Services, Multiple Servers)**

Trong Hình 8-3, mọi thứ trở nên thú vị hơn nhiều. Nhiều dịch vụ đang hợp tác để cung cấp các khả năng cho người dùng của chúng ta, và các dịch vụ đó đang chạy trên nhiều máy chủ—dù là vật lý hay ảo. Làm thế nào để bạn tìm thấy lỗi bạn đang tìm kiếm trong hàng ngàn dòng nhật ký trên nhiều máy chủ? Làm thế nào để bạn xác định xem một máy chủ có hoạt động sai, hay đó là một vấn đề hệ thống? Và làm thế nào để bạn truy ngược một lỗi được tìm thấy sâu trong một chuỗi cuộc gọi giữa nhiều máy chủ và tìm ra nguyên nhân của nó?

![alt text](<images/Screenshot from 2025-09-29 13-11-38.png>)

**Hình 8-3.** *Nhiều dịch vụ hợp tác được phân phối trên nhiều máy chủ*

Câu trả lời là **thu thập và tổng hợp trung tâm** càng nhiều càng tốt những gì chúng ta có thể có được, từ nhật ký đến các chỉ số ứng dụng.

##### **Nhật ký, Nhật ký, và Thêm Nhật ký nữa… (Logs, Logs, and Yet More Logs…)**

Bây giờ số lượng máy chủ chúng ta đang chạy đang trở thành một thách thức. Việc ghép kênh `SSH` để truy xuất nhật ký có lẽ sẽ không còn hiệu quả nữa, và không có màn hình nào đủ lớn để bạn có các terminal mở trên mọi máy chủ. Thay vào đó, chúng ta đang tìm cách sử dụng các hệ thống con chuyên dụng để lấy nhật ký của chúng ta và làm cho chúng có sẵn một cách tập trung. Một ví dụ của điều này là **logstash**, có thể phân tích cú pháp nhiều định dạng tệp nhật ký và có thể gửi chúng đến các hệ thống hạ nguồn để điều tra thêm.

**Kibana** là một hệ thống dựa trên ElasticSearch để xem nhật ký, được minh họa trong Hình 8-4. Bạn có thể sử dụng cú pháp truy vấn để tìm kiếm thông qua các nhật ký, cho phép bạn làm những việc như hạn chế các phạm vi thời gian và ngày hoặc sử dụng các biểu thức chính quy để tìm các chuỗi phù hợp. Kibana thậm chí có thể tạo ra các biểu đồ từ các nhật ký bạn gửi cho nó, cho phép bạn xem nhanh có bao nhiêu lỗi đã được tạo ra theo thời gian, chẳng hạn.

![alt text](<images/Screenshot from 2025-09-29 13-11-46.png>)

**Hình 8-4.** *Sử dụng Kibana để xem các nhật ký được tổng hợp*

##### **Theo dõi Chỉ số trên Nhiều Dịch vụ (Metric Tracking Across Multiple Services)**

Cũng như với thách thức của việc xem xét các nhật ký cho các máy chủ khác nhau, chúng ta cần xem xét các cách tốt hơn để thu thập và xem các chỉ số của mình. Có thể khó biết *tốt* trông như thế nào khi chúng ta đang xem xét các chỉ số cho một hệ thống phức tạp hơn. Trang web của chúng ta đang thấy gần 50 mã lỗi `HTTP` 4XX mỗi giây. Điều đó có tệ không? Tải CPU trên dịch vụ danh mục đã tăng 20% kể từ bữa trưa; có điều gì đó không ổn sao? Bí quyết để biết khi nào nên hoảng loạn và khi nào nên thư giãn là thu thập các chỉ số về cách hệ thống của bạn hoạt động trong một khoảng thời gian đủ dài để các mẫu rõ ràng xuất hiện.

Trong một môi trường phức tạp hơn, chúng ta sẽ cấp phát các phiên bản mới của các dịch vụ của mình khá thường xuyên, vì vậy chúng ta muốn hệ thống chúng ta chọn làm cho việc thu thập các chỉ số từ các máy chủ mới trở nên rất dễ dàng. Chúng ta sẽ muốn có thể xem xét một chỉ số được tổng hợp cho toàn bộ hệ thống—ví dụ, tải CPU trung bình—nhưng chúng ta cũng sẽ muốn tổng hợp chỉ số đó cho tất cả các phiên bản của một dịch vụ nhất định, hoặc thậm chí cho một phiên bản duy nhất của dịch vụ đó. Điều đó có nghĩa là chúng ta sẽ cần có khả năng liên kết siêu dữ liệu với chỉ số để cho phép chúng ta suy ra cấu trúc này.

**Graphite** là một hệ thống làm cho điều này rất dễ dàng. Nó phơi bày một API rất đơn giản và cho phép bạn gửi các chỉ số theo thời gian thực. Sau đó, nó cho phép bạn truy vấn các chỉ số đó để tạo ra các biểu đồ và các hiển thị khác để xem điều gì đang xảy ra. Cách nó xử lý khối lượng cũng rất thú vị. Hiệu quả, bạn cấu hình nó để bạn giảm độ phân giải của các chỉ số cũ hơn để đảm bảo khối lượng không trở nên quá lớn. Vì vậy, ví dụ, tôi có thể ghi lại CPU cho các máy chủ của mình mỗi 10 giây một lần trong 10 phút qua, sau đó một mẫu được tổng hợp mỗi phút cho ngày qua, xuống đến có lẽ một mẫu mỗi 30 phút cho vài năm qua. Bằng cách này, bạn có thể lưu trữ thông tin về cách hệ thống của bạn đã hoạt động trong một khoảng thời gian dài mà không cần dung lượng lưu trữ khổng lồ.

Graphite cũng cho phép bạn tổng hợp trên các mẫu, hoặc đi sâu vào một chuỗi duy nhất, vì vậy bạn có thể xem thời gian phản hồi cho toàn bộ hệ thống của mình, một nhóm các dịch vụ, hoặc một phiên bản duy nhất. Nếu Graphite không hoạt động cho bạn vì bất kỳ lý do gì, hãy đảm bảo bạn có được các khả năng tương tự trong bất kỳ công cụ nào khác bạn chọn. Và chắc chắn hãy đảm bảo bạn có thể truy cập vào dữ liệu thô để cung cấp báo cáo hoặc bảng điều khiển của riêng bạn nếu bạn cần.

Một lợi ích chính khác của việc hiểu các xu hướng của bạn là khi nói đến **lập kế hoạch năng lực (capacity planning)**. Chúng ta có đang đạt đến giới hạn của mình không? Bao lâu nữa chúng ta sẽ cần thêm máy chủ? Trong quá khứ khi chúng ta mang các máy chủ vật lý, đây thường là một công việc hàng năm. Trong thời đại mới của điện toán theo yêu cầu được cung cấp bởi các nhà cung cấp **cơ sở hạ tầng như một dịch vụ (Infrastructure as a Service - IaaS)**, bây giờ chúng ta có thể mở rộng quy mô lên hoặc xuống trong vài phút, nếu không muốn nói là vài giây. Điều này có nghĩa là nếu chúng ta hiểu các mẫu sử dụng của mình, chúng ta có thể đảm bảo chúng ta chỉ có đủ cơ sở hạ tầng để phục vụ nhu cầu của mình. Càng thông minh hơn trong việc theo dõi các xu hướng của chúng ta và biết phải làm gì với chúng, các hệ thống của chúng ta càng hiệu quả về chi phí và phản ứng nhanh hơn.

*(Phần tiếp theo sẽ thảo luận về "Service Metrics", "Synthetic Monitoring", và "Correlation IDs".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với các phần còn lại của Chương 8, khám phá cách thu thập các chỉ số cụ thể của dịch vụ và các kỹ thuật giám sát nâng cao.

---

##### **Chỉ số Dịch vụ (Service Metrics)**

Các hệ điều hành chúng ta chạy trên đó tạo ra một số lượng lớn các chỉ số cho chúng ta, như bạn sẽ thấy ngay khi bạn cài đặt `collectd` trên một máy Linux và trỏ nó vào Graphite. Tương tự như vậy, các hệ thống con hỗ trợ như Nginx hoặc Varnish phơi bày các thông tin hữu ích như thời gian phản hồi hoặc tỷ lệ truy cập bộ nhớ đệm. Nhưng còn dịch vụ của riêng bạn thì sao?

Tôi thực sự đề nghị các dịch vụ của bạn tự phơi bày các chỉ số cơ bản. Ở mức tối thiểu, đối với một dịch vụ web, bạn có lẽ nên phơi bày các chỉ số như **thời gian phản hồi** và **tỷ lệ lỗi**—quan trọng nếu máy chủ của bạn không được đặt sau một máy chủ web đang làm điều này cho bạn. Nhưng bạn thực sự nên đi xa hơn. Ví dụ, dịch vụ tài khoản của chúng ta có thể muốn phơi bày số lần khách hàng xem các đơn đặt hàng trong quá khứ của họ, hoặc cửa hàng web của bạn có thể muốn ghi lại số tiền đã kiếm được trong ngày qua.

Tại sao chúng ta quan tâm đến điều này? Chà, vì một số lý do.
*   **Thứ nhất**, có một câu ngạn ngữ cũ rằng 80% các tính năng phần mềm không bao giờ được sử dụng. Bây giờ tôi không thể bình luận về mức độ chính xác của con số đó, nhưng với tư cách là một người đã phát triển phần mềm gần 20 năm, tôi biết rằng tôi đã dành rất nhiều thời gian cho các tính năng không bao giờ thực sự được sử dụng. Sẽ không tuyệt sao nếu biết chúng là gì?
*   **Thứ hai**, chúng ta ngày càng giỏi hơn trong việc phản ứng với cách người dùng của chúng ta đang sử dụng hệ thống của mình để tìm ra cách cải thiện nó. Các chỉ số thông báo cho chúng ta về cách hệ thống của chúng ta hoạt động chỉ có thể giúp chúng ta ở đây. Chúng ta đẩy ra một phiên bản mới của trang web, và thấy rằng số lượng tìm kiếm theo thể loại đã tăng lên đáng kể trên dịch vụ danh mục. Đó là một vấn đề, hay là điều được mong đợi?
*   **Cuối cùng**, chúng ta không bao giờ có thể biết dữ liệu nào sẽ hữu ích! Đã không biết bao nhiêu lần tôi muốn thu thập dữ liệu để giúp tôi hiểu một điều gì đó chỉ sau khi cơ hội làm điều đó đã qua từ lâu. Tôi có xu hướng thiên về việc phơi bày mọi thứ và dựa vào hệ thống chỉ số của mình để xử lý điều này sau.

Các thư viện tồn tại cho một số nền tảng khác nhau cho phép các dịch vụ của chúng ta gửi các chỉ số đến các hệ thống tiêu chuẩn. Thư viện **Metrics** của Codahale là một ví dụ về thư viện như vậy cho JVM. Nó cho phép bạn lưu trữ các chỉ số dưới dạng bộ đếm, bộ định thời gian, hoặc máy đo; hỗ trợ các chỉ số giới hạn thời gian (vì vậy bạn có thể chỉ định các chỉ số như "số lượng đơn đặt hàng trong năm phút qua"); và cũng đi kèm với hỗ trợ ngay từ đầu để gửi dữ liệu đến Graphite và các hệ thống tổng hợp và báo cáo khác.

##### **Giám sát Tổng hợp (Synthetic Monitoring)**

Chúng ta có thể cố gắng tìm ra liệu một dịch vụ có *khỏe mạnh* hay không bằng cách, ví dụ, quyết định mức CPU tốt là gì, hoặc điều gì tạo nên một thời gian phản hồi chấp nhận được. Nếu hệ thống giám sát của chúng ta phát hiện ra rằng các giá trị thực tế nằm ngoài mức an toàn này, chúng ta có thể kích hoạt một cảnh báo—một điều mà một công cụ như Nagios có thừa khả năng.

Tuy nhiên, theo nhiều cách, các giá trị này cách một bước so với những gì chúng ta thực sự muốn theo dõi—cụ thể là, **hệ thống có đang hoạt động không?** Càng có nhiều tương tác phức tạp giữa các dịch vụ, chúng ta càng bị tách rời khỏi việc thực sự trả lời câu hỏi đó. Vậy điều gì sẽ xảy ra nếu các hệ thống giám sát của chúng ta được lập trình để hoạt động một chút giống như người dùng của chúng ta, và có thể báo cáo lại nếu có sự cố xảy ra?

Lần đầu tiên tôi làm điều này là vào năm 2005. Tôi là một phần của một đội nhỏ của ThoughtWorks đang xây dựng một hệ thống cho một ngân hàng đầu tư. Trong suốt ngày giao dịch, rất nhiều sự kiện đến đại diện cho những thay đổi trên thị trường. Công việc của chúng tôi là phản ứng với những thay đổi này, và xem xét tác động lên danh mục đầu tư của ngân hàng. Chúng tôi đang làm việc dưới một số thời hạn khá eo hẹp, vì mục tiêu là đã thực hiện tất cả các tính toán của mình trong vòng chưa đầy 10 giây sau khi sự kiện đến. Bản thân hệ thống bao gồm khoảng năm dịch vụ riêng biệt, ít nhất một trong số đó đang chạy trên một lưới điện toán, trong số những thứ khác, đang thu thập các chu kỳ CPU không sử dụng trên khoảng 250 máy chủ máy tính để bàn trong trung tâm khôi phục thảm họa của ngân hàng.

Số lượng các bộ phận chuyển động trong hệ thống có nghĩa là rất nhiều nhiễu đang được tạo ra từ nhiều chỉ số cấp thấp hơn mà chúng tôi đang thu thập. Chúng tôi không có lợi ích của việc mở rộng quy mô dần dần hoặc để hệ thống chạy trong vài tháng để hiểu *tốt* trông như thế nào đối với các chỉ số như tỷ lệ CPU của chúng tôi hoặc thậm chí là độ trễ của một số thành phần riêng lẻ. Cách tiếp cận của chúng tôi là tạo ra các sự kiện giả để định giá một phần của danh mục đầu tư không được ghi vào các hệ thống hạ nguồn. Mỗi phút hoặc lâu hơn, chúng tôi có Nagios chạy một công việc dòng lệnh chèn một sự kiện giả vào một trong các hàng đợi của chúng tôi. Hệ thống của chúng tôi nhặt nó lên và chạy tất cả các tính toán khác nhau giống như bất kỳ công việc nào khác, ngoại trừ kết quả xuất hiện trong sổ rác, chỉ được sử dụng để kiểm thử. Nếu việc định giá lại không được nhìn thấy trong một thời gian nhất định, Nagios sẽ báo cáo điều này như một vấn đề.

Sự kiện giả này chúng tôi đã tạo ra là một ví dụ về **giao dịch tổng hợp (synthetic transaction)**. Chúng tôi đã sử dụng giao dịch tổng hợp này để đảm bảo rằng hệ thống đang hoạt động về mặt ngữ nghĩa, đó là lý do tại sao kỹ thuật này thường được gọi là **giám sát ngữ nghĩa (semantic monitoring)**.

Trong thực tế, tôi đã thấy việc sử dụng các giao dịch tổng hợp để thực hiện giám sát ngữ nghĩa như thế này là một chỉ báo tốt hơn nhiều về các vấn đề trong các hệ thống so với việc cảnh báo trên các chỉ số cấp thấp hơn. Tuy nhiên, chúng không thay thế nhu cầu về các chỉ số cấp thấp hơn—chúng ta vẫn sẽ muốn chi tiết đó khi chúng ta cần tìm ra *tại sao* giám sát ngữ nghĩa của chúng ta lại báo cáo một vấn đề.

##### **Triển khai Giám sát Ngữ nghĩa (Implementing Semantic Monitoring)**

Bây giờ trong quá khứ, việc triển khai giám sát ngữ nghĩa là một nhiệm vụ khá khó khăn. Nhưng thế giới đã thay đổi, và các phương tiện để làm điều này nằm trong tầm tay của chúng ta! Bạn đang chạy các bài kiểm thử cho các hệ thống của mình, phải không? Nếu không, hãy đọc **Chương 7** và quay lại. Xong cả chưa? Tốt!

Nếu chúng ta xem xét các bài kiểm thử chúng ta có để kiểm thử một dịch vụ nhất định từ đầu đến cuối, hoặc thậm chí toàn bộ hệ thống của chúng ta từ đầu đến cuối, chúng ta có phần lớn những gì chúng ta cần để triển khai giám sát ngữ nghĩa. Hệ thống của chúng ta đã phơi bày các móc cần thiết để khởi chạy bài kiểm thử và kiểm tra kết quả. Vậy tại sao không chỉ chạy một tập hợp con của các bài kiểm thử này, một cách liên tục, như một cách để giám sát hệ thống của chúng ta?

Tất nhiên, có một số điều chúng ta cần làm.
*   **Thứ nhất**, chúng ta cần cẩn thận về các yêu cầu dữ liệu của các bài kiểm thử của mình. Chúng ta có thể cần tìm một cách để các bài kiểm thử của mình thích ứng với dữ liệu trực tiếp khác nhau nếu điều này thay đổi theo thời gian, hoặc thiết lập một nguồn dữ liệu khác. Ví dụ, chúng ta có thể có một bộ người dùng giả mà chúng ta sử dụng trong sản xuất với một bộ dữ liệu đã biết.
*   **Tương tự như vậy**, chúng ta phải đảm bảo rằng chúng ta không vô tình kích hoạt các tác dụng phụ không lường trước được. Một người bạn đã kể cho tôi một câu chuyện về một công ty thương mại điện tử đã vô tình chạy các bài kiểm thử của mình chống lại các hệ thống đặt hàng sản xuất của họ. Họ đã không nhận ra sai lầm của mình cho đến khi một số lượng lớn máy giặt đến trụ sở chính.

##### **ID Tương quan (Correlation IDs)**

Với một số lượng lớn các dịch vụ tương tác để cung cấp bất kỳ khả năng nào cho người dùng cuối, một cuộc gọi khởi tạo duy nhất có thể tạo ra nhiều cuộc gọi dịch vụ hạ nguồn hơn. Ví dụ, hãy xem xét ví dụ về một khách hàng được đăng ký. Khách hàng điền tất cả các chi tiết của mình vào một biểu mẫu và nhấp vào gửi. Phía sau hậu trường, chúng ta kiểm tra tính hợp lệ của các chi tiết thẻ tín dụng với dịch vụ thanh toán của mình, nói chuyện với dịch vụ bưu chính của chúng ta để gửi đi một gói chào mừng, và gửi một email chào mừng bằng dịch vụ email của chúng ta. Bây giờ điều gì sẽ xảy ra nếu cuộc gọi đến dịch vụ thanh toán cuối cùng tạo ra một lỗi kỳ lạ? Chúng ta sẽ nói dài về việc xử lý lỗi trong **Chương 11**, nhưng hãy xem xét khó khăn của việc chẩn đoán những gì đã xảy ra.

Nếu chúng ta xem xét các nhật ký, dịch vụ duy nhất ghi lại một lỗi là dịch vụ thanh toán của chúng ta. Nếu chúng ta may mắn, chúng ta có thể tìm ra yêu cầu nào đã gây ra sự cố, và chúng ta thậm chí có thể xem xét các tham số của cuộc gọi. Bây giờ hãy xem xét rằng đây là một ví dụ đơn giản, và một yêu cầu khởi tạo duy nhất có thể tạo ra một chuỗi các cuộc gọi hạ nguồn và có thể là các sự kiện được kích hoạt được xử lý một cách bất đồng bộ. Làm thế nào chúng ta có thể tái tạo lại luồng các cuộc gọi để tái tạo và khắc phục sự cố? Thường thì những gì chúng ta cần là xem lỗi đó trong bối cảnh rộng hơn của cuộc gọi khởi tạo; nói cách khác, chúng ta muốn **theo dõi chuỗi cuộc gọi (trace the call chain)** ngược dòng, giống như chúng ta làm với một dấu vết ngăn xếp (`stack trace`).

Một cách tiếp cận có thể hữu ích ở đây là sử dụng các **ID tương quan (correlation IDs)**. Khi cuộc gọi đầu tiên được thực hiện, bạn tạo ra một `GUID` cho cuộc gọi. Điều này sau đó được truyền đi cho tất cả các cuộc gọi tiếp theo, như được thấy trong Hình 8-5, và có thể được đưa vào nhật ký của bạn một cách có cấu trúc, giống như bạn đã làm với các thành phần như cấp độ nhật ký hoặc ngày tháng. Với các công cụ tổng hợp nhật ký phù hợp, bạn sau đó sẽ có thể theo dõi sự kiện đó trên toàn bộ hệ thống của mình:

```
15-02-2014 16:01:01 Web-Frontend INFO [abc-123] Register
15-02-2014 16:01:02 RegisterService INFO [abc-123] RegisterCustomer ...
15-02-2014 16:01:03 PostalSystem INFO [abc-123] SendWelcomePack ...
15-02-2014 16:01:03 EmailSystem INFO [abc-123] SendWelcomeEmail ...
15-02-2014 16:01:03 PaymentGateway ERROR [abc-123] ValidatePayment ...
```

![alt text](<images/Screenshot from 2025-09-29 13-12-03.png>)

**Hình 8-5.** *Sử dụng các ID tương quan để theo dõi các chuỗi cuộc gọi trên nhiều dịch vụ*

Tất nhiên, bạn sẽ cần đảm bảo rằng mỗi dịch vụ biết cách truyền ID tương quan. Đây là nơi bạn cần phải chuẩn hóa và mạnh mẽ hơn trong việc thực thi điều này trên toàn hệ thống của mình. Nhưng một khi bạn đã làm điều này, bạn thực sự có thể tạo ra các công cụ để theo dõi tất cả các loại tương tác. Các công cụ như vậy có thể hữu ích trong việc theo dõi các cơn bão sự kiện (`event storms`), các trường hợp góc cạnh kỳ lạ, hoặc thậm chí xác định các giao dịch đặc biệt tốn kém, vì bạn có thể hình dung toàn bộ chuỗi các cuộc gọi.

Phần mềm như **Zipkin** cũng có thể theo dõi các cuộc gọi trên nhiều ranh giới hệ thống. Dựa trên các ý tưởng từ hệ thống theo dõi của riêng Google, Dapper, Zipkin có thể cung cấp việc theo dõi rất chi tiết các cuộc gọi giữa các dịch vụ, cùng với một giao diện người dùng để giúp trình bày dữ liệu. Cá nhân tôi, tôi đã thấy các yêu cầu của Zipkin hơi nặng nề, đòi hỏi các máy khách tùy chỉnh và các hệ thống thu thập hỗ trợ. Với việc bạn đã muốn tổng hợp nhật ký cho các mục đích khác, có cảm giác đơn giản hơn nhiều nếu thay vào đó sử dụng dữ liệu bạn đã thu thập hơn là phải lắp đặt thêm các nguồn dữ liệu bổ sung. Điều đó nói rằng, nếu bạn thấy rằng bạn cần một công cụ nâng cao hơn để theo dõi các cuộc gọi giữa các dịch vụ như thế này, bạn có thể muốn xem xét chúng.

Một trong những vấn đề thực sự với các ID tương quan là bạn thường không biết mình cần một cái cho đến khi bạn đã có một vấn đề chỉ có thể được chẩn đoán nếu bạn có ID ngay từ đầu! Điều này đặc biệt có vấn đề, vì việc trang bị thêm các ID tương quan là rất khó khăn; bạn cần xử lý chúng một cách chuẩn hóa để có thể dễ dàng tái tạo lại các chuỗi cuộc gọi. Mặc dù có vẻ như là công việc bổ sung ở phía trước, tôi thực sự đề nghị bạn xem xét việc đưa chúng vào càng sớm càng tốt, đặc biệt nếu hệ thống của bạn sẽ sử dụng các mẫu kiến trúc hướng sự kiện, có thể dẫn đến một số hành vi nổi lên kỳ lạ.

Việc cần phải xử lý các tác vụ như truyền các ID tương quan một cách nhất quán có thể là một lập luận mạnh mẽ cho việc sử dụng các thư viện `wrapper` máy khách mỏng dùng chung. Ở một quy mô nhất định, trở nên khó khăn để đảm bảo rằng mọi người đều đang gọi các dịch vụ hạ nguồn đúng cách và thu thập đúng loại dữ liệu. Chỉ cần một dịch vụ giữa chừng trong chuỗi quên làm điều này là bạn đã mất thông tin quan trọng. Nếu bạn quyết định tạo ra một thư viện máy khách nội bộ để làm cho những việc như thế này hoạt động ngay từ đầu, hãy đảm bảo bạn giữ nó rất mỏng và không bị ràng buộc vào bất kỳ dịch vụ sản xuất cụ thể nào. Ví dụ, nếu bạn đang sử dụng `HTTP` làm giao thức cơ bản để giao tiếp, chỉ cần gói một thư viện máy khách `HTTP` tiêu chuẩn, thêm vào đó mã để đảm bảo bạn truyền các ID tương quan trong các tiêu đề.

##### **Thác đổ (The Cascade)**

Các lỗi xếp tầng có thể đặc biệt nguy hiểm. Hãy tưởng tượng một tình huống mà kết nối mạng giữa trang web cửa hàng âm nhạc của chúng ta và dịch vụ danh mục bị hỏng. Bản thân các dịch vụ có vẻ khỏe mạnh, nhưng chúng không thể nói chuyện với nhau. Nếu chúng ta chỉ nhìn vào sức khỏe của từng dịch vụ riêng lẻ, chúng ta sẽ không biết có vấn đề. Sử dụng giám sát tổng hợp—ví dụ, để bắt chước một khách hàng tìm kiếm một bài hát—sẽ phát hiện ra vấn đề. Nhưng chúng ta cũng cần phải báo cáo về thực tế là một dịch vụ không thể thấy một dịch vụ khác để xác định nguyên nhân của vấn đề.

Do đó, việc giám sát các điểm tích hợp giữa các hệ thống là chìa khóa. Mỗi phiên bản dịch vụ nên theo dõi và phơi bày sức khỏe của các phụ thuộc hạ nguồn của nó, từ cơ sở dữ liệu đến các dịch vụ hợp tác khác. Bạn cũng nên cho phép thông tin này được tổng hợp để cung cấp cho bạn một bức tranh tổng thể. Bạn sẽ muốn xem thời gian phản hồi của các cuộc gọi hạ nguồn, và cũng phát hiện xem nó có đang báo lỗi hay không.

Như chúng ta sẽ thảo luận nhiều hơn trong **Chương 11**, bạn có thể sử dụng các thư viện để triển khai một **bộ ngắt mạch (circuit breaker)** xung quanh các cuộc gọi mạng để giúp bạn xử lý các lỗi xếp tầng một cách duyên dáng hơn, cho phép bạn giảm cấp chức năng của hệ thống một cách duyên dáng hơn. Một số thư viện này, chẳng hạn như Hystrix cho JVM, cũng làm một công việc tốt trong việc cung cấp các khả năng giám sát này cho bạn.

##### **Tiêu chuẩn hóa (Standardization)**

Như chúng ta đã đề cập trước đây, một trong những hành động cân bằng liên tục mà bạn sẽ cần phải thực hiện là nơi cho phép các quyết định được đưa ra một cách hẹp hòi cho một dịch vụ duy nhất so với nơi bạn cần phải tiêu chuẩn hóa trên toàn hệ thống của mình. Theo quan điểm của tôi, giám sát là một lĩnh vực mà việc tiêu chuẩn hóa là vô cùng quan trọng. Với các dịch vụ hợp tác theo nhiều cách khác nhau để cung cấp các khả năng cho người dùng sử dụng nhiều giao diện, bạn cần phải xem xét hệ thống một cách toàn diện.

Bạn nên cố gắng viết nhật ký của mình ra ở một định dạng tiêu chuẩn. Bạn chắc chắn muốn có tất cả các chỉ số của mình ở một nơi, và bạn có thể muốn có một danh sách các tên tiêu chuẩn cho các chỉ số của mình; sẽ rất khó chịu nếu một dịch vụ có một chỉ số được gọi là `ResponseTime`, và một dịch vụ khác có một chỉ số được gọi là `RspTimeSecs`, khi chúng có cùng ý nghĩa.

Như mọi khi với việc tiêu chuẩn hóa, các công cụ có thể giúp ích. Như tôi đã nói trước đây, chìa khóa là làm cho việc làm đúng trở nên dễ dàng—vậy tại sao không cung cấp các hình ảnh máy ảo được cấu hình sẵn với `logstash` và `collectd` sẵn sàng hoạt động, cùng với các thư viện ứng dụng cho phép bạn nói chuyện với Graphite một cách thực sự dễ dàng?

##### **Xem xét Đối tượng (Consider the Audience)**

Tất cả dữ liệu này chúng ta đang thu thập là vì một mục đích. Cụ thể hơn, chúng ta đang thu thập tất cả dữ liệu này cho những người khác nhau để giúp họ làm công việc của mình; dữ liệu này trở thành một lời kêu gọi hành động. Một số dữ liệu này cần phải kích hoạt một cuộc gọi hành động ngay lập tức cho đội hỗ trợ của chúng ta—ví dụ, trong trường hợp một trong các bài kiểm thử giám sát tổng hợp của chúng ta thất bại. Dữ liệu khác, như thực tế là tải CPU của chúng ta đã tăng 2% trong tuần qua, có khả năng chỉ được quan tâm khi chúng ta đang lập kế hoạch năng lực. Tương tự như vậy, sếp của bạn có lẽ sẽ muốn biết ngay lập tức rằng doanh thu đã giảm 25% sau lần phát hành cuối cùng, nhưng có lẽ không cần phải bị đánh thức vì các tìm kiếm cho "Justin Bieber" đã tăng 5% trong giờ qua.

Những gì mọi người của chúng ta muốn xem và phản ứng ngay bây giờ khác với những gì họ cần khi đi sâu vào chi tiết. Vì vậy, đối với loại người sẽ xem xét dữ liệu này, hãy xem xét những điều sau:

*   **Họ cần biết điều gì ngay bây giờ**
*   **Họ có thể muốn gì sau này**
*   **Họ thích tiêu thụ dữ liệu như thế nào**

Cảnh báo về những điều họ cần biết ngay bây giờ. Tạo các màn hình hiển thị lớn, dễ nhìn với thông tin này đặt ở góc phòng. Cung cấp cho họ quyền truy cập dễ dàng vào dữ liệu họ cần biết sau này. Và dành thời gian với họ để biết họ muốn tiêu thụ dữ liệu như thế nào. Một cuộc thảo luận về tất cả các sắc thái liên quan đến việc hiển thị đồ họa thông tin định lượng chắc chắn nằm ngoài phạm vi của cuốn sách này, nhưng một nơi tuyệt vời để bắt đầu là cuốn sách xuất sắc của Stephen Few, *Information Dashboard Design: Displaying Data for At-a-Glance Monitoring* (Analytics Press).

##### **Tương lai (The Future)**

Tôi đã thấy nhiều tổ chức nơi các chỉ số được phân chia vào các hệ thống khác nhau. Các chỉ số cấp ứng dụng, như số lượng đơn đặt hàng được đặt, cuối cùng lại nằm trong một hệ thống phân tích độc quyền như Omniture, thường chỉ có sẵn cho một số bộ phận được chọn của doanh nghiệp, hoặc cuối cùng lại nằm trong kho dữ liệu đáng sợ, hay còn gọi là nơi dữ liệu đến để chết. Việc báo cáo từ các hệ thống như vậy thường không có sẵn theo thời gian thực, mặc dù điều đó đang bắt đầu thay đổi. Trong khi đó, các chỉ số hệ thống như thời gian phản hồi, tỷ lệ lỗi, và tải CPU được lưu trữ trong các hệ thống mà các đội vận hành có thể truy cập. Các hệ thống này thường cho phép báo cáo theo thời gian thực, vì thông thường mục đích của chúng là để kích động một cuộc gọi hành động ngay lập tức.

Trong lịch sử, ý tưởng rằng chúng ta có thể tìm ra các chỉ số kinh doanh chính một hoặc hai ngày sau là ổn, vì thông thường chúng ta không thể phản ứng đủ nhanh với dữ liệu này để làm bất cứ điều gì về nó. Tuy nhiên, bây giờ, chúng ta hoạt động trong một thế giới mà nhiều người trong chúng ta có thể và thực sự đẩy ra nhiều bản phát hành mỗi ngày. Các đội bây giờ tự đo lường không phải bằng số điểm họ hoàn thành, mà thay vào đó tối ưu hóa cho thời gian cần thiết để mã đi từ máy tính xách tay đến sản xuất. Trong một môi trường như vậy, chúng ta cần tất cả các chỉ số của mình trong tầm tay để thực hiện hành động đúng đắn. Trớ trêu thay, chính các hệ thống lưu trữ các chỉ số kinh doanh thường không được điều chỉnh để truy cập dữ liệu ngay lập tức, nhưng các hệ thống vận hành của chúng ta thì có.

Vậy tại sao không xử lý các chỉ số hoạt động và kinh doanh theo cùng một cách? Cuối cùng, cả hai loại đều quy về các sự kiện nói rằng *một cái gì đó đã xảy ra tại X*. Vì vậy, nếu chúng ta có thể thống nhất các hệ thống chúng ta sử dụng để thu thập, tổng hợp, và lưu trữ các sự kiện này, và làm cho chúng có sẵn để báo cáo, chúng ta sẽ kết thúc với một kiến trúc đơn giản hơn nhiều.

**Riemann** là một máy chủ sự kiện cho phép tổng hợp và định tuyến các sự kiện khá tiên tiến và có thể là một phần của một giải pháp như vậy. **Suro** là đường ống dữ liệu của Netflix và hoạt động trong một không gian tương tự. Suro được sử dụng một cách rõ ràng để xử lý cả các chỉ số liên quan đến hành vi của người dùng, và nhiều dữ liệu hoạt động hơn như nhật ký ứng dụng. Dữ liệu này sau đó có thể được gửi đến nhiều hệ thống khác nhau, như Storm để phân tích thời gian thực, Hadoop để xử lý hàng loạt ngoại tuyến, hoặc Kibana để phân tích nhật ký.

Nhiều tổ chức đang đi theo một hướng hoàn toàn khác: từ việc có các chuỗi công cụ chuyên dụng cho các loại chỉ số khác nhau sang các hệ thống định tuyến sự kiện chung hơn có khả năng mở rộng đáng kể. Các hệ thống này quản lý để cung cấp nhiều sự linh hoạt hơn, đồng thời thực sự đơn giản hóa kiến trúc của chúng ta.

---
#### **Tóm tắt (Summary)**

Vậy là, chúng ta đã đề cập rất nhiều ở đây! Tôi sẽ cố gắng tóm tắt chương này thành một số lời khuyên dễ theo dõi.

**Đối với mỗi dịch vụ:**

*   **Theo dõi thời gian phản hồi gửi đến** ở mức tối thiểu. Một khi bạn đã làm điều đó, hãy tiếp tục với tỷ lệ lỗi và sau đó bắt đầu làm việc trên các chỉ số cấp ứng dụng.
*   **Theo dõi sức khỏe của tất cả các phản hồi hạ nguồn**, ở mức tối thiểu bao gồm thời gian phản hồi của các cuộc gọi hạ nguồn, và tốt nhất là theo dõi tỷ lệ lỗi. Các thư viện như Hystrix có thể giúp ích ở đây.
*   **Tiêu chuẩn hóa** về cách thức và nơi các chỉ số được thu thập.
*   **Ghi nhật ký** vào một vị trí tiêu chuẩn, ở một định dạng tiêu chuẩn nếu có thể. Việc tổng hợp sẽ là một cực hình nếu mỗi dịch vụ sử dụng một bố cục khác nhau!
*   **Giám sát hệ điều hành cơ bản** để bạn có thể theo dõi các tiến trình lừa đảo và lập kế hoạch năng lực.

**Đối với hệ thống:**

*   **Tổng hợp các chỉ số cấp máy chủ** như CPU cùng với các chỉ số cấp ứng dụng.
*   **Đảm bảo công cụ lưu trữ chỉ số của bạn cho phép tổng hợp** ở cấp hệ thống hoặc dịch vụ, và đi sâu vào các máy chủ riêng lẻ.
*   **Đảm bảo công cụ lưu trữ chỉ số của bạn cho phép bạn duy trì dữ liệu đủ lâu** để hiểu các xu hướng trong hệ thống của bạn.
*   **Có một công cụ duy nhất, có thể truy vấn** để tổng hợp và lưu trữ nhật ký.
*   **Xem xét mạnh mẽ việc tiêu chuẩn hóa** việc sử dụng các ID tương quan.
*   **Hiểu những gì đòi hỏi một cuộc gọi hành động**, và cấu trúc cảnh báo và bảng điều khiển một cách tương ứng.
*   **Điều tra khả năng thống nhất cách bạn tổng hợp tất cả các chỉ số khác nhau của mình** bằng cách xem liệu một công cụ như Suro hoặc Riemann có phù hợp với bạn hay không.

Tôi cũng đã cố gắng phác thảo hướng đi mà việc giám sát đang di chuyển: từ các hệ thống chuyên biệt để chỉ làm một việc, sang các hệ thống xử lý sự kiện chung cho phép bạn xem xét hệ thống của mình một cách toàn diện hơn. Đây là một không gian thú vị và đang nổi lên, và trong khi một cuộc điều tra đầy đủ nằm ngoài phạm vi của cuốn sách này, hy vọng tôi đã cung cấp cho bạn đủ để bắt đầu. Nếu bạn muốn biết thêm, tôi sẽ đi sâu vào một số ý tưởng này và nhiều hơn nữa trong ấn phẩm trước đây của tôi, *Lightweight Systems for Realtime Monitoring* (O’Reilly).

Trong chương tiếp theo, chúng ta sẽ có một cái nhìn toàn diện khác về các hệ thống của mình để xem xét một số lợi thế độc đáo—và những thách thức—mà các kiến trúc chi tiết có thể cung cấp trong lĩnh vực bảo mật.

Chắc chắn rồi! Chúng ta sẽ bắt đầu Chương 9: "Bảo mật (Security)". Chương này sẽ cung cấp một cái nhìn tổng quan về các khía cạnh bảo mật cần cân nhắc khi xây dựng hệ thống microservice.

---

### **9. Bảo mật (Security)**

Chúng ta đã trở nên quen thuộc với những câu chuyện về các vụ vi phạm bảo mật của các hệ thống quy mô lớn dẫn đến việc dữ liệu của chúng ta bị lộ cho đủ loại nhân vật đáng ngờ. Nhưng gần đây, những sự kiện như các tiết lộ của Edward Snowden đã khiến chúng ta càng nhận thức rõ hơn về giá trị của dữ liệu mà các công ty nắm giữ về chúng ta, và giá trị của dữ liệu mà chúng ta nắm giữ cho khách hàng của mình trong các hệ thống chúng ta xây dựng. Chương này sẽ cung cấp một cái nhìn tổng quan ngắn gọn về một số khía cạnh của bảo mật bạn nên xem xét khi thiết kế các hệ thống của mình. Mặc dù không nhằm mục đích toàn diện, nó sẽ trình bày một số tùy chọn chính có sẵn cho bạn và cung cấp cho bạn một điểm khởi đầu cho nghiên cứu sâu hơn của riêng bạn.

Chúng ta cần suy nghĩ về việc dữ liệu của chúng ta cần được bảo vệ như thế nào khi **đang truyền (in transit)** từ điểm này đến điểm khác, và nó cần được bảo vệ như thế nào **khi nghỉ (at rest)**. Chúng ta cần suy nghĩ về bảo mật của các hệ điều hành cơ bản của chúng ta, và cả mạng lưới của chúng ta nữa. Có quá nhiều điều cần suy nghĩ, và quá nhiều điều chúng ta có thể làm! Vậy chúng ta cần bao nhiêu bảo mật? Làm thế nào để chúng ta tìm ra *đủ* bảo mật là bao nhiêu?

Nhưng chúng ta cũng cần suy nghĩ về yếu tố con người. Làm thế nào để chúng ta biết một người là ai, và họ có thể làm gì? Và điều này liên quan như thế nào đến cách các máy chủ của chúng ta nói chuyện với nhau? Hãy bắt đầu từ đó.

#### **Xác thực và Ủy quyền (Authentication and Authorization)**

**Xác thực (Authentication)** và **ủy quyền (authorization)** là các khái niệm cốt lõi khi nói đến con người và những thứ tương tác với hệ thống của chúng ta. Trong bối cảnh bảo mật, **xác thực** là quá trình mà chúng ta xác nhận rằng một bên là người mà cô ấy nói. Đối với một con người, bạn thường xác thực một người dùng bằng cách yêu cầu họ nhập tên người dùng và mật khẩu của họ. Chúng ta giả định rằng chỉ có cô ấy mới có quyền truy cập vào thông tin này, và do đó người nhập thông tin này phải là cô ấy. Tất nhiên, các hệ thống phức tạp hơn cũng tồn tại. Điện thoại của tôi bây giờ cho phép tôi sử dụng dấu vân tay của mình để xác nhận rằng tôi là người tôi nói. Nói chung, khi chúng ta nói một cách trừu tượng về ai hoặc cái gì đang được xác thực, chúng ta gọi bên đó là **chủ thể (principal)**.

**Ủy quyền** là cơ chế mà chúng ta ánh xạ từ một chủ thể đến hành động chúng ta đang cho phép cô ấy thực hiện. Thường thì, khi một chủ thể được xác thực, chúng ta sẽ được cung cấp thông tin về cô ấy sẽ giúp chúng ta quyết định chúng ta nên để cô ấy làm gì. Ví dụ, chúng ta có thể được cho biết cô ấy làm việc ở bộ phận hoặc văn phòng nào—những mẩu thông tin mà hệ thống của chúng ta có thể sử dụng để quyết định cô ấy có thể và không thể làm gì.

Đối với các ứng dụng `monolithic` đơn lẻ, việc chính ứng dụng đó xử lý việc xác thực và ủy quyền cho bạn là phổ biến. Ví dụ, Django, một `framework` web Python, đi kèm với quản lý người dùng ngay từ đầu. Tuy nhiên, khi nói đến các hệ thống phân tán, chúng ta cần suy nghĩ về các sơ đồ nâng cao hơn. Chúng ta không muốn mọi người phải đăng nhập riêng cho các hệ thống khác nhau, sử dụng một tên người dùng và mật khẩu khác nhau cho mỗi hệ thống. Mục đích là có một danh tính duy nhất mà chúng ta có thể xác thực một lần.

##### **Các Triển khai Đăng nhập Một lần (Single Sign-On) Phổ biến**

Một cách tiếp cận phổ biến để xác thực và ủy quyền là sử dụng một loại giải pháp **đăng nhập một lần (single sign-on - SSO)** nào đó. **SAML**, là triển khai thống trị trong không gian doanh nghiệp, và **OpenID Connect** đều cung cấp các khả năng trong lĩnh vực này. Chúng ít nhiều sử dụng các khái niệm cốt lõi giống nhau, mặc dù thuật ngữ có khác nhau một chút. Các thuật ngữ được sử dụng ở đây là từ SAML.

Khi một chủ thể cố gắng truy cập một tài nguyên (như một giao diện dựa trên web), cô ấy được chuyển hướng để xác thực với một **nhà cung cấp danh tính (identity provider)**. Điều này có thể yêu cầu cô ấy cung cấp tên người dùng và mật khẩu, hoặc có thể sử dụng một cái gì đó nâng cao hơn như xác thực hai yếu tố. Một khi nhà cung cấp danh tính hài lòng rằng chủ thể đã được xác thực, nó sẽ cung cấp thông tin cho **nhà cung cấp dịch vụ (service provider)**, cho phép nó quyết định liệu có cấp cho cô ấy quyền truy cập vào tài nguyên hay không.

Nhà cung cấp danh tính này có thể là một hệ thống được lưu trữ bên ngoài, hoặc một cái gì đó bên trong tổ chức của bạn. Ví dụ, Google cung cấp một nhà cung cấp danh tính OpenID Connect. Tuy nhiên, đối với các doanh nghiệp, việc có nhà cung cấp danh tính của riêng bạn là phổ biến, có thể được liên kết với dịch vụ thư mục của công ty bạn. Một dịch vụ thư mục có thể là một cái gì đó như Giao thức Truy cập Thư mục Nhẹ (`Lightweight Directory Access Protocol - LDAP`) hoặc Active Directory. Các hệ thống này cho phép bạn lưu trữ thông tin về các chủ thể, chẳng hạn như vai trò họ đóng trong tổ chức. Thường thì, dịch vụ thư mục và nhà cung cấp danh tính là một và giống nhau, trong khi đôi khi chúng riêng biệt nhưng được liên kết. Ví dụ, Okta là một nhà cung cấp danh tính SAML được lưu trữ xử lý các tác vụ như xác thực hai yếu tố, nhưng có thể liên kết với các dịch vụ thư mục của công ty bạn như nguồn chân lý.

SAML là một tiêu chuẩn dựa trên `SOAP`, và được biết đến là khá phức tạp để làm việc mặc dù có các thư viện và công cụ có sẵn để hỗ trợ nó. OpenID Connect là một tiêu chuẩn đã nổi lên như một triển khai cụ thể của OAuth 2.0, dựa trên cách Google và những người khác xử lý SSO. Nó sử dụng các cuộc gọi `REST` đơn giản hơn, và theo quan điểm của tôi có khả năng sẽ thâm nhập vào các doanh nghiệp do sự dễ sử dụng được cải thiện của nó. Trở ngại lớn nhất của nó hiện nay là sự thiếu hụt các nhà cung cấp danh tính hỗ trợ nó. Đối với một trang web hướng tới công chúng, bạn có thể OK khi sử dụng Google làm nhà cung cấp của mình, nhưng đối với các hệ thống nội bộ hoặc các hệ thống nơi bạn muốn kiểm soát và khả năng hiển thị nhiều hơn về cách thức và nơi dữ liệu của bạn được cài đặt, bạn sẽ muốn có nhà cung cấp danh tính nội bộ của riêng mình. Tại thời điểm viết bài, OpenAM và Gluu là hai trong số rất ít tùy chọn có sẵn trong không gian này, so với vô số tùy chọn cho SAML (bao gồm cả Active Directory, dường như có ở mọi nơi). Cho đến khi và trừ khi các nhà cung cấp danh tính hiện có bắt đầu hỗ trợ OpenID Connect, sự phát triển của nó có thể bị giới hạn ở những tình huống mà mọi người hài lòng khi sử dụng một nhà cung cấp danh tính công khai.

Vì vậy, trong khi tôi nghĩ rằng OpenID Connect là tương lai, rất có thể sẽ mất một thời gian để nó được áp dụng rộng rãi.

##### **Cổng Đăng nhập Một lần (Single Sign-On Gateway)**

Trong một thiết lập microservice, mỗi dịch vụ có thể quyết định xử lý việc chuyển hướng đến, và bắt tay với, nhà cung cấp danh tính. Rõ ràng, điều này có thể có nghĩa là rất nhiều công việc trùng lặp. Một thư viện dùng chung có thể giúp ích, nhưng chúng ta sẽ phải cẩn thận để tránh sự ghép nối có thể đến từ mã dùng chung. Điều này cũng sẽ không giúp ích gì nếu bạn có nhiều `technology stack` khác nhau.

Thay vì để mỗi dịch vụ quản lý việc bắt tay với nhà cung cấp danh tính của bạn, bạn có thể sử dụng một **cổng (gateway)** để hoạt động như một `proxy`, nằm giữa các dịch vụ của bạn và thế giới bên ngoài (như được hiển thị trong Hình 9-1). Ý tưởng là chúng ta có thể tập trung hóa hành vi chuyển hướng người dùng và thực hiện việc bắt tay chỉ ở một nơi.

![alt text](<images/Screenshot from 2025-09-29 13-12-15.png>)

**Hình 9-1.** *Sử dụng một cổng để xử lý SSO*

Tuy nhiên, chúng ta vẫn cần giải quyết vấn đề về cách dịch vụ hạ nguồn nhận được thông tin về các chủ thể, chẳng hạn như tên người dùng của họ hoặc vai trò họ đóng. Nếu bạn đang sử dụng `HTTP`, nó có thể điền các tiêu đề với thông tin này. **Shibboleth** là một công cụ có thể làm điều này cho bạn, và tôi đã thấy nó được sử dụng với Apache để mang lại hiệu quả lớn trong việc xử lý tích hợp với các nhà cung cấp danh tính dựa trên SAML.

Một vấn đề khác là nếu chúng ta đã quyết định giao trách nhiệm xác thực cho một cổng, có thể khó hơn để lý giải về cách một microservice hoạt động khi xem xét nó một cách cô lập. Hãy nhớ trong **Chương 7**, nơi chúng ta đã khám phá một số thách thức trong việc tái tạo các môi trường giống sản xuất? Nếu bạn đi theo con đường cổng, hãy đảm bảo rằng các nhà phát triển của bạn có thể khởi chạy các dịch vụ của họ đằng sau một cổng mà không cần quá nhiều công việc.

Một vấn đề cuối cùng với cách tiếp cận này là nó có thể ru bạn vào một cảm giác an toàn giả tạo. Tôi thích ý tưởng về **phòng thủ theo chiều sâu (defense in depth)**—từ chu vi mạng, đến mạng con, đến tường lửa, đến máy, đến hệ điều hành, đến phần cứng cơ bản. Bạn có khả năng thực hiện các biện pháp bảo mật ở tất cả các điểm này, một số trong đó chúng ta sẽ đề cập ngay sau đây. Tôi đã thấy một số người đặt tất cả trứng của họ vào một giỏ, dựa vào cổng để xử lý mọi bước cho họ. Và tất cả chúng ta đều biết điều gì sẽ xảy ra khi chúng ta có một điểm lỗi duy nhất…

Rõ ràng bạn có thể sử dụng cổng này để làm những việc khác. Ví dụ, nếu sử dụng một lớp các phiên bản Apache đang chạy Shibboleth, bạn cũng có thể quyết định chấm dứt `HTTPS` ở cấp độ này, chạy phát hiện xâm nhập, và vân vân. Tuy nhiên, hãy cẩn thận. Các lớp cổng có xu hướng đảm nhận ngày càng nhiều chức năng, mà bản thân nó có thể trở thành một điểm ghép nối khổng lồ. Và càng có nhiều chức năng, bề mặt tấn công (`attack surface`) càng lớn.

##### **Ủy quyền Chi tiết (Fine-Grained Authorization)**

Một cổng có thể có khả năng cung cấp xác thực thô khá hiệu quả. Ví dụ, nó có thể ngăn chặn quyền truy cập của bất kỳ người dùng nào chưa đăng nhập vào ứng dụng trợ giúp. Giả sử cổng của chúng ta có thể trích xuất các thuộc tính về chủ thể như là kết quả của việc xác thực, nó có thể đưa ra các quyết định tinh tế hơn. Ví dụ, việc đặt mọi người vào các nhóm, hoặc gán cho họ các vai trò là phổ biến. Chúng ta có thể sử dụng thông tin này để hiểu họ có thể làm gì. Vì vậy, đối với ứng dụng trợ giúp, chúng ta có thể chỉ cho phép truy cập cho các chủ thể có một vai trò cụ thể (ví dụ: `STAFF`). Ngoài việc cho phép (hoặc không cho phép) truy cập vào các tài nguyên hoặc điểm cuối cụ thể, tuy nhiên, chúng ta cần để phần còn lại cho chính microservice; nó sẽ cần phải đưa ra các quyết định sâu hơn về những hoạt động nào được cho phép.

Quay trở lại ứng dụng trợ giúp của chúng ta: chúng ta có cho phép bất kỳ nhân viên nào xem bất kỳ và tất cả các chi tiết không? Rất có thể, chúng ta sẽ có các vai trò khác nhau tại nơi làm việc. Ví dụ, một chủ thể trong nhóm `CALL_CENTER` có thể được phép xem bất kỳ mẩu thông tin nào về một khách hàng ngoại trừ chi tiết thanh toán của anh ta. Chủ thể cũng có thể có thể cấp các khoản hoàn tiền, nhưng số tiền đó có thể bị giới hạn. Tuy nhiên, một người có vai trò `CALL_CENTER_TEAM_LEADER` có thể có thể cấp các khoản hoàn tiền lớn hơn.

Những quyết định này cần phải là cục bộ đối với microservice được đề cập. Tôi đã thấy mọi người sử dụng các thuộc tính khác nhau được cung cấp bởi các nhà cung cấp danh tính theo những cách khủng khiếp, sử dụng các vai trò rất chi tiết như `CALL_CENTER_50_DOLLAR_REFUND`, nơi họ cuối cùng đặt thông tin cụ thể cho một phần của hành vi một hệ thống của chúng ta vào các dịch vụ thư mục của họ. Đây là một cơn ác mộng để duy trì và mang lại rất ít phạm vi cho các dịch vụ của chúng ta có vòng đời độc lập của riêng chúng, vì đột nhiên một phần thông tin về cách một dịch vụ hoạt động lại nằm ở nơi khác, có lẽ trong một hệ thống được quản lý bởi một bộ phận khác của tổ chức.

Thay vào đó, hãy ủng hộ các vai trò thô, được mô hình hóa xung quanh cách tổ chức của bạn hoạt động. Quay trở lại các chương đầu tiên, hãy nhớ rằng chúng ta đang xây dựng phần mềm để phù hợp với cách tổ chức của chúng ta hoạt động. Vì vậy, hãy sử dụng các vai trò của bạn theo cách này.

*(Phần tiếp theo sẽ thảo luận về "Service-to-Service Authentication and Authorization".)*

Chắc chắn rồi! Chúng ta sẽ tiếp tục với một khía cạnh bảo mật quan trọng khác trong hệ thống microservice: xác thực và ủy quyền giữa các dịch vụ với nhau.

---

#### **Xác thực và Ủy quyền giữa các Dịch vụ (Service-to-Service Authentication and Authorization)**

Cho đến thời điểm này, chúng ta đã sử dụng thuật ngữ **chủ thể (principal)** để mô tả bất cứ thứ gì có thể xác thực và được ủy quyền để làm việc, nhưng các ví dụ của chúng ta thực ra là về con người sử dụng máy tính. Nhưng còn về các chương trình, hoặc các dịch vụ khác, xác thực với nhau thì sao?

##### **Cho phép Mọi thứ bên trong Chu vi (Allow Everything Inside the Perimeter)**

Tùy chọn đầu tiên của chúng ta có thể là chỉ cần giả định rằng bất kỳ cuộc gọi nào đến một dịch vụ được thực hiện từ bên trong **chu vi (perimeter)** của chúng ta đều được tin cậy một cách ngầm định.

Tùy thuộc vào độ nhạy cảm của dữ liệu, điều này có thể ổn. Một số tổ chức cố gắng đảm bảo an ninh tại chu vi của mạng của họ, và do đó giả định rằng họ không cần phải làm bất cứ điều gì khác khi hai dịch vụ nói chuyện với nhau. Tuy nhiên, nếu một kẻ tấn công xâm nhập vào mạng của bạn, bạn sẽ có rất ít sự bảo vệ chống lại một cuộc tấn công **người đứng giữa (man-in-the-middle)** điển hình. Nếu kẻ tấn công quyết định chặn và đọc dữ liệu đang được gửi, thay đổi dữ liệu mà bạn không biết, hoặc thậm chí trong một số trường hợp giả vờ là thứ bạn đang nói chuyện, bạn có thể không biết nhiều về nó.

Đây là hình thức phổ biến nhất của sự tin tưởng bên trong chu vi mà tôi thấy trong các tổ chức. Họ có thể quyết định chạy lưu lượng truy cập này qua `HTTPS`, nhưng họ không làm gì nhiều hơn. Tôi không nói rằng đó là một điều tốt! Đối với hầu hết các tổ chức tôi thấy sử dụng mô hình này, tôi lo lắng rằng mô hình tin cậy ngầm định không phải là một quyết định có ý thức, mà là mọi người không nhận thức được những rủi ro ngay từ đầu.

##### **Xác thực Cơ bản HTTP(S) (HTTP(S) Basic Authentication)**

Xác thực Cơ bản `HTTP` cho phép một máy khách gửi một tên người dùng và mật khẩu trong một tiêu đề `HTTP` tiêu chuẩn. Máy chủ sau đó có thể kiểm tra các chi tiết này và xác nhận rằng máy khách được phép truy cập dịch vụ. Lợi thế ở đây là đây là một giao thức cực kỳ được hiểu rõ và được hỗ trợ tốt. Vấn đề là việc làm điều này trên `HTTP` là rất có vấn đề, vì tên người dùng và mật khẩu không được gửi một cách an toàn. Bất kỳ bên trung gian nào cũng có thể xem xét thông tin trong tiêu đề và thấy dữ liệu. Do đó, Xác thực Cơ bản `HTTP` thường nên được sử dụng trên `HTTPS`.

Khi sử dụng `HTTPS`, máy khách có được sự đảm bảo mạnh mẽ rằng máy chủ mà nó đang nói chuyện là người mà máy khách nghĩ. Nó cũng cung cấp cho chúng ta sự bảo vệ bổ sung chống lại việc mọi người nghe lén lưu lượng truy cập giữa máy khách và máy chủ hoặc gây rối với `payload`.

Máy chủ cần phải quản lý các chứng chỉ `SSL` của riêng mình, điều này có thể trở nên có vấn đề khi nó đang quản lý nhiều máy. Một số tổ chức đảm nhận quy trình cấp chứng chỉ của riêng họ, đó là một gánh nặng hành chính và hoạt động bổ sung. Các công cụ xung quanh việc quản lý điều này một cách tự động không hề trưởng thành như chúng có thể, và không chỉ là quy trình cấp phát bạn phải xử lý. Các chứng chỉ tự ký không dễ bị thu hồi, và do đó đòi hỏi nhiều suy nghĩ hơn xung quanh các kịch bản thảm họa. Hãy xem liệu bạn có thể tránh tất cả công việc này bằng cách tránh tự ký hoàn toàn.

Một nhược điểm khác là lưu lượng truy cập được gửi qua `SSL` không thể được lưu vào bộ nhớ đệm bởi các `proxy` ngược như Varnish hoặc Squid. Điều này có nghĩa là nếu bạn cần lưu vào bộ nhớ đệm lưu lượng truy cập, nó sẽ phải được thực hiện hoặc bên trong máy chủ hoặc bên trong máy khách. Bạn có thể khắc phục điều này bằng cách có một bộ cân bằng tải chấm dứt lưu lượng `SSL`, và để bộ nhớ đệm nằm sau bộ cân bằng tải.

Chúng ta cũng phải suy nghĩ về những gì xảy ra nếu chúng ta đang sử dụng một giải pháp `SSO` hiện có, như SAML, đã có quyền truy cập vào tên người dùng và mật khẩu. Chúng ta có muốn xác thực dịch vụ cơ bản của mình sử dụng cùng một bộ thông tin xác thực, cho phép chúng ta một quy trình để cấp và thu hồi chúng không? Chúng ta có thể làm điều này bằng cách để dịch vụ nói chuyện với cùng một dịch vụ thư mục hỗ trợ giải pháp `SSO` của chúng ta. Hoặc, chúng ta có thể tự lưu trữ tên người dùng và mật khẩu bên trong dịch vụ, nhưng sau đó chúng ta có nguy cơ nhân bản hành vi.

Một lưu ý: trong cách tiếp cận này, tất cả những gì máy chủ biết là máy khách có tên người dùng và mật khẩu. Chúng ta không biết liệu thông tin này có đến từ một máy chúng ta mong đợi hay không; nó có thể đến từ bất kỳ ai trên mạng của chúng ta.

##### **Sử dụng SAML hoặc OpenID Connect**

Nếu bạn đã sử dụng SAML hoặc OpenID Connect làm sơ đồ xác thực và ủy quyền của mình, bạn có thể chỉ cần sử dụng nó cho các tương tác từ dịch vụ đến dịch vụ. Nếu bạn đang sử dụng một cổng, bạn cũng sẽ cần phải định tuyến tất cả lưu lượng truy cập trong mạng qua cổng, nhưng nếu mỗi dịch vụ đang tự xử lý việc tích hợp, cách tiếp cận này sẽ hoạt động ngay từ đầu. Lợi thế ở đây là bạn đang tận dụng cơ sở hạ tầng hiện có, và có thể tập trung hóa tất cả các điều khiển truy cập dịch vụ của mình trong một máy chủ thư mục trung tâm. Chúng ta vẫn cần phải định tuyến điều này qua `HTTPS` nếu chúng ta muốn tránh các cuộc tấn công người đứng giữa.

Các máy khách có một bộ thông tin xác thực mà chúng sử dụng để xác thực với nhà cung cấp danh tính, và dịch vụ nhận được thông tin nó cần để quyết định về bất kỳ xác thực chi tiết nào.

Điều này có nghĩa là bạn sẽ cần một tài khoản cho các máy khách của mình, đôi khi được gọi là **tài khoản dịch vụ (service account)**. Nhiều tổ chức sử dụng cách tiếp cận này khá phổ biến. Tuy nhiên, một lời cảnh báo: nếu bạn định tạo các tài khoản dịch vụ, hãy cố gắng giữ cho việc sử dụng chúng hẹp. Vì vậy, hãy xem xét mỗi microservice có bộ thông tin xác thực riêng của nó. Điều này làm cho việc thu hồi/thay đổi quyền truy cập dễ dàng hơn nếu thông tin xác thực bị xâm phạm, vì bạn chỉ cần thu hồi bộ thông tin xác thực đã bị ảnh hưởng.

Tuy nhiên, có một vài nhược điểm khác.
*   **Thứ nhất**, giống như với Xác thực Cơ bản, chúng ta cần lưu trữ thông tin xác thực của mình một cách an toàn: tên người dùng và mật khẩu sống ở đâu? Máy khách sẽ cần phải tìm một cách an toàn nào đó để lưu trữ dữ liệu này.
*   **Vấn đề khác** là một số công nghệ trong không gian này để thực hiện xác thực khá tẻ nhạt để viết mã. Đặc biệt, SAML làm cho việc triển khai một máy khách trở thành một công việc đau đớn. OpenID Connect có một quy trình làm việc đơn giản hơn, nhưng như chúng ta đã thảo luận trước đó, nó không được hỗ trợ tốt như vậy.

##### **Chứng chỉ Máy khách (Client Certificates)**

Một cách tiếp cận khác để xác nhận danh tính của một máy khách là sử dụng các khả năng trong **Bảo mật Lớp Vận chuyển (Transport Layer Security - TLS)**, người kế nhiệm của `SSL`, dưới dạng các **chứng chỉ máy khách**. Ở đây, mỗi máy khách có một chứng chỉ X.509 được cài đặt được sử dụng để thiết lập một liên kết giữa máy khách và máy chủ. Máy chủ có thể xác minh tính xác thực của chứng chỉ máy khách, cung cấp các đảm bảo mạnh mẽ rằng máy khách là hợp lệ.

Những thách thức hoạt động ở đây trong việc quản lý chứng chỉ thậm chí còn nặng nề hơn so với chỉ sử dụng các chứng chỉ phía máy chủ. Không chỉ là một số vấn đề cơ bản của việc tạo và quản lý một số lượng lớn hơn các chứng chỉ; mà là với tất cả sự phức tạp xung quanh chính các chứng chỉ, bạn có thể mong đợi phải dành rất nhiều thời gian để cố gắng chẩn đoán tại sao một dịch vụ không chấp nhận những gì bạn tin là một chứng chỉ máy khách hoàn toàn hợp lệ. Và sau đó chúng ta phải xem xét khó khăn của việc thu hồi và cấp lại các chứng chỉ nếu điều tồi tệ nhất xảy ra. Việc sử dụng các chứng chỉ ký tự đại diện (`wildcard certificates`) có thể giúp ích, nhưng sẽ không giải quyết được tất cả các vấn đề. Gánh nặng bổ sung này có nghĩa là bạn sẽ tìm cách sử dụng kỹ thuật này khi bạn đặc biệt quan tâm đến độ nhạy cảm của dữ liệu đang được gửi, hoặc nếu bạn đang gửi dữ liệu qua các mạng bạn không kiểm soát hoàn toàn. Vì vậy, bạn có thể quyết định bảo mật việc giao tiếp dữ liệu rất quan trọng giữa các bên được gửi qua Internet, chẳng hạn.

*(Phần tiếp theo sẽ thảo luận về "HMAC Over HTTP", "API Keys", và "The Deputy Problem".)*

Chắc chắn rồi! Chúng ta sẽ đi tiếp với các phương pháp xác thực dịch vụ-tới-dịch vụ khác và khám phá một vấn đề bảo mật tinh vi được gọi là "Confused Deputy Problem".

---

##### **HMAC qua HTTP (HMAC Over HTTP)**

Như chúng ta đã thảo luận trước đó, việc sử dụng Xác thực Cơ bản qua `HTTP` thuần túy không phải là một điều quá hợp lý nếu chúng ta lo lắng về việc tên người dùng và mật khẩu bị xâm phạm. Giải pháp thay thế truyền thống là định tuyến lưu lượng truy cập qua `HTTPS`, nhưng có một số nhược điểm. Ngoài việc quản lý các chứng chỉ, chi phí của lưu lượng `HTTPS` có thể gây thêm gánh nặng cho các máy chủ (mặc dù thành thật mà nói, điều này có tác động thấp hơn so với vài năm trước), và lưu lượng truy cập không thể dễ dàng được lưu vào bộ nhớ đệm (`cached`).

Một cách tiếp cận thay thế, được sử dụng rộng rãi bởi các API S3 của Amazon cho AWS và trong các phần của đặc tả OAuth, là sử dụng một **mã thông điệp dựa trên băm (hash-based messaging code - HMAC)** để ký yêu cầu.

Với HMAC, nội dung yêu cầu cùng với một khóa riêng tư được băm, và kết quả băm được gửi cùng với yêu cầu. Máy chủ sau đó sử dụng bản sao của riêng mình của khóa riêng tư và nội dung yêu cầu để tạo lại hàm băm. Nếu nó khớp, nó cho phép yêu cầu. Điều hay ở đây là nếu một người đứng giữa gây rối với yêu cầu, thì hàm băm sẽ không khớp và máy chủ biết rằng yêu cầu đã bị giả mạo. Và khóa riêng tư không bao giờ được gửi trong yêu cầu, vì vậy nó không thể bị xâm phạm khi đang truyền! Lợi ích bổ sung là lưu lượng truy cập này sau đó có thể được lưu vào bộ nhớ đệm dễ dàng hơn, và chi phí tạo ra các hàm băm có thể thấp hơn so với việc xử lý lưu lượng `HTTPS` (mặc dù kết quả của bạn có thể khác nhau).

Có ba nhược điểm đối với cách tiếp cận này.
1.  **Thứ nhất**, cả máy khách và máy chủ đều cần một bí mật được chia sẻ cần được truyền đạt bằng cách nào đó. Họ chia sẻ nó như thế nào? Nó có thể được mã hóa cứng ở cả hai đầu, nhưng sau đó bạn có vấn đề về việc thu hồi quyền truy cập nếu bí mật bị xâm phạm. Nếu bạn truyền khóa này qua một giao thức thay thế nào đó, thì bạn cần đảm bảo rằng giao thức đó cũng rất an toàn!
2.  **Thứ hai**, đây là một mẫu hình (`pattern`), không phải là một tiêu chuẩn, và do đó có những cách khác nhau để triển khai nó. Kết quả là, có sự khan hiếm các triển khai tốt, mở và có thể sử dụng được của cách tiếp cận này. Nói chung, nếu cách tiếp cận này hấp dẫn bạn, thì hãy đọc thêm để hiểu các cách khác nhau mà nó được thực hiện. Tôi sẽ đi xa đến mức chỉ cần xem cách Amazon làm điều này cho S3 và sao chép cách tiếp cận của họ, đặc biệt là sử dụng một hàm băm hợp lý với một khóa đủ dài như SHA-256. **JSON web tokens (JWT)** cũng đáng để xem xét, vì chúng triển khai một cách tiếp cận rất giống và dường như đang được chấp nhận. Nhưng hãy nhận thức được khó khăn của việc làm đúng những thứ này. Đồng nghiệp của tôi đã làm việc với một đội đang triển khai JWT của riêng họ, đã bỏ qua một kiểm tra Boolean duy nhất, và đã làm vô hiệu hóa toàn bộ mã xác thực của họ! Hy vọng theo thời gian, chúng ta sẽ thấy nhiều triển khai thư viện có thể tái sử dụng hơn.
3.  **Cuối cùng**, hãy hiểu rằng cách tiếp cận này chỉ đảm bảo rằng không có bên thứ ba nào đã thao túng yêu cầu và bản thân khóa riêng tư vẫn là riêng tư. Phần còn lại của dữ liệu trong yêu cầu vẫn sẽ hiển thị cho các bên đang rình mò trên mạng.

##### **Khóa API (API Keys)**

Tất cả các API công khai từ các dịch vụ như Twitter, Google, Flickr, và AWS đều sử dụng **khóa API (API keys)**. Khóa API cho phép một dịch vụ xác định ai đang thực hiện một cuộc gọi, và đặt các giới hạn về những gì họ có thể làm. Thường thì các giới hạn vượt ra ngoài việc chỉ cấp quyền truy cập vào một tài nguyên, và có thể mở rộng đến các hành động như **giới hạn tốc độ (rate-limiting)** các người gọi cụ thể để bảo vệ chất lượng dịch vụ cho những người khác.

Khi nói đến việc sử dụng các khóa API để xử lý cách tiếp cận microservice-to-microservice của riêng bạn, cơ chế chính xác về cách nó hoạt động sẽ phụ thuộc vào công nghệ bạn sử dụng. Một số hệ thống sử dụng một khóa API duy nhất được chia sẻ, và sử dụng một cách tiếp cận tương tự như HMAC vừa được mô tả. Một cách tiếp cận phổ biến hơn là sử dụng một cặp khóa công khai và riêng tư. Thông thường, bạn sẽ quản lý các khóa một cách tập trung, giống như cách chúng ta sẽ quản lý danh tính của mọi người một cách tập trung. Mô hình **cổng (gateway)** rất phổ biến trong không gian này.

Một phần của sự phổ biến của chúng xuất phát từ thực tế là các khóa API tập trung vào sự dễ sử dụng cho các chương trình. So với việc xử lý một cái bắt tay SAML, xác thực dựa trên khóa API đơn giản và thẳng thắn hơn nhiều.

Các khả năng chính xác của các hệ thống khác nhau, và bạn có nhiều tùy chọn trong cả không gian thương mại và mã nguồn mở. Một số sản phẩm chỉ xử lý việc trao đổi khóa API và một số quản lý khóa cơ bản. Các công cụ khác cung cấp mọi thứ lên đến và bao gồm cả giới hạn tốc độ, kiếm tiền, danh mục API, và các hệ thống khám phá.

Một số hệ thống API cho phép bạn bắc cầu các khóa API đến các dịch vụ thư mục hiện có. Điều này sẽ cho phép bạn cấp các khóa API cho các chủ thể (đại diện cho người hoặc hệ thống) trong tổ chức của bạn, và kiểm soát vòng đời của các khóa đó theo cùng một cách bạn quản lý thông tin xác thực thông thường của họ. Điều này mở ra khả năng cho phép truy cập vào các dịch vụ của bạn theo những cách khác nhau nhưng vẫn giữ cùng một nguồn chân lý—ví dụ, sử dụng SAML để xác thực con người cho SSO, và sử dụng các khóa API để giao tiếp từ dịch vụ đến dịch vụ, như được hiển thị trong Hình 9-2.

![alt text](<images/Screenshot from 2025-09-29 13-12-24.png>)

**Hình 9-2.** *Sử dụng các dịch vụ thư mục để đồng bộ hóa thông tin chủ thể giữa một SSO và một cổng API*

##### **Vấn đề Đại diện Bị nhầm lẫn (The Deputy Problem)**

Việc một chủ thể xác thực với một microservice nhất định là đủ đơn giản. Nhưng điều gì sẽ xảy ra nếu dịch vụ đó sau đó cần thực hiện các cuộc gọi bổ sung để hoàn thành một hoạt động? Hãy xem Hình 9-3, minh họa trang web mua sắm trực tuyến của MusicCorp. Cửa hàng trực tuyến của chúng ta là một giao diện người dùng JavaScript dựa trên trình duyệt. Nó thực hiện các cuộc gọi đến một ứng dụng cửa hàng phía máy chủ, sử dụng mẫu `backends-for-frontends` mà chúng ta đã mô tả trong **Chương 4**. Các cuộc gọi được thực hiện giữa trình duyệt và các cuộc gọi máy chủ có thể được xác thực bằng SAML hoặc OpenID Connect hoặc tương tự. Cho đến nay thì tốt.

Khi tôi đăng nhập, tôi có thể nhấp vào một liên kết để xem chi tiết của một đơn đặt hàng. Để hiển thị thông tin, chúng ta cần lấy lại đơn đặt hàng ban đầu từ dịch vụ đặt hàng, nhưng chúng ta cũng muốn tra cứu thông tin vận chuyển cho đơn đặt hàng. Vì vậy, việc nhấp vào liên kết đến `/orderStatus/12345` khiến cửa hàng trực tuyến khởi tạo một cuộc gọi từ dịch vụ cửa hàng trực tuyến đến cả dịch vụ đặt hàng và dịch vụ vận chuyển để yêu cầu các chi tiết đó. Nhưng liệu các dịch vụ hạ nguồn này có nên chấp nhận các cuộc gọi từ cửa hàng trực tuyến không? Chúng ta có thể áp dụng một lập trường tin cậy ngầm định—rằng vì cuộc gọi đến từ bên trong chu vi của chúng ta, nên nó OK. Chúng ta thậm chí có thể sử dụng các chứng chỉ hoặc các khóa API để xác nhận rằng vâng, thực sự là cửa hàng trực tuyến đang yêu cầu thông tin này. Nhưng liệu điều này có đủ không?

![alt text](<images/Screenshot from 2025-09-29 13-12-31.png>)

**Hình 9-3.** *Một ví dụ nơi một đại diện bị nhầm lẫn có thể xuất hiện*

Có một loại lỗ hổng được gọi là **vấn đề đại diện bị nhầm lẫn (confused deputy problem)**, mà trong bối cảnh của giao tiếp từ dịch vụ đến dịch vụ đề cập đến một tình huống mà một bên độc hại có thể lừa một dịch vụ đại diện thực hiện các cuộc gọi đến một dịch vụ hạ nguồn thay mặt cho anh ta mà anh ta không nên có thể. Ví dụ, với tư cách là một khách hàng, khi tôi đăng nhập vào hệ thống mua sắm trực tuyến, tôi có thể xem chi tiết tài khoản của mình. Điều gì sẽ xảy ra nếu tôi có thể lừa giao diện người dùng mua sắm trực tuyến thực hiện một yêu cầu cho chi tiết của người khác, có lẽ bằng cách thực hiện một cuộc gọi với thông tin đăng nhập đã đăng nhập của tôi?

Trong ví dụ này, điều gì ngăn tôi yêu cầu các đơn đặt hàng không phải của tôi? Một khi đã đăng nhập, tôi có thể bắt đầu gửi các yêu cầu cho các đơn đặt hàng khác không phải của tôi để xem liệu tôi có thể nhận được thông tin hữu ích hay không. Chúng ta có thể cố gắng bảo vệ chống lại điều này bên trong chính cửa hàng trực tuyến, bằng cách kiểm tra đơn đặt hàng dành cho ai và từ chối nó nếu ai đó đang yêu cầu những thứ họ không nên. Tuy nhiên, nếu chúng ta có nhiều ứng dụng khác nhau hiển thị thông tin này, chúng ta có thể có khả năng nhân bản logic này ở nhiều nơi.

Chúng ta có thể định tuyến các yêu cầu trực tiếp từ giao diện người dùng đến dịch vụ đặt hàng và cho phép nó xác thực yêu cầu, nhưng sau đó chúng ta gặp phải các nhược điểm khác nhau mà chúng ta đã thảo luận trong **Chương 4**. Hoặc, khi cửa hàng trực tuyến gửi yêu cầu đến dịch vụ đặt hàng, nó có thể không chỉ nêu rõ đơn đặt hàng nào nó muốn, mà còn thay mặt cho ai nó đang yêu cầu. Một số sơ đồ xác thực cho phép chúng ta truyền thông tin xác thực của chủ thể ban đầu xuống hạ nguồn, mặc dù với SAML, đây là một cơn ác mộng, liên quan đến các xác nhận SAML lồng nhau về mặt kỹ thuật có thể đạt được—nhưng khó đến mức không ai từng làm điều này. Tất nhiên, điều này có thể trở nên phức tạp hơn nữa. Hãy tưởng tượng nếu các dịch vụ mà cửa hàng trực tuyến nói chuyện lần lượt thực hiện nhiều cuộc gọi hạ nguồn hơn. Chúng ta phải đi bao xa trong việc xác thực sự tin tưởng cho tất cả các đại diện đó?

Thật không may, vấn đề này không có câu trả lời đơn giản, bởi vì nó không phải là một vấn đề đơn giản. Tuy nhiên, hãy nhận thức rằng nó tồn tại. Tùy thuộc vào độ nhạy cảm của hoạt động được đề cập, bạn có thể phải lựa chọn giữa tin cậy ngầm định, xác minh danh tính của người gọi, hoặc yêu cầu người gọi cung cấp thông tin xác thực của chủ thể ban đầu.

*(Phần tiếp theo và cuối cùng của chương này sẽ thảo luận về "Securing Data at Rest".)*

Chắc chắn rồi! Chúng ta sẽ đi tiếp với phần cuối cùng của Chương 9, tập trung vào việc bảo vệ dữ liệu khi nó không được sử dụng, hay còn gọi là "Securing Data at Rest".

---

#### **Bảo mật Dữ liệu khi Nghỉ (Securing Data at Rest)**

Dữ liệu nằm yên là một trách nhiệm pháp lý, đặc biệt nếu nó nhạy cảm. Hy vọng rằng chúng ta đã làm mọi thứ có thể để đảm bảo kẻ tấn công không thể xâm nhập vào mạng của chúng ta, và cũng đảm bảo rằng chúng không thể xâm nhập vào các ứng dụng hoặc hệ điều hành của chúng ta để có quyền truy cập vào các tệp cơ bản. Tuy nhiên, chúng ta cần phải chuẩn bị cho trường hợp chúng làm được—**phòng thủ theo chiều sâu (defense in depth)** là chìa khóa.

Nhiều vụ vi phạm bảo mật đình đám liên quan đến dữ liệu khi nghỉ bị kẻ tấn công chiếm đoạt, và dữ liệu đó có thể đọc được bởi kẻ tấn công. Điều này hoặc là do dữ liệu được lưu trữ ở dạng không được mã hóa, hoặc do cơ chế được sử dụng để bảo vệ dữ liệu có một lỗ hổng cơ bản.

Các cơ chế mà thông tin an toàn có thể được bảo vệ là rất nhiều và đa dạng, nhưng bất kể bạn chọn cách tiếp cận nào, có một số điều chung cần ghi nhớ.

##### **Đi theo những gì Đã biết (Go with the Well Known)**

Cách dễ nhất bạn có thể làm hỏng việc mã hóa dữ liệu là cố gắng tự mình triển khai các thuật toán mã hóa của riêng bạn, hoặc thậm chí cố gắng triển khai của người khác. Bất kể bạn sử dụng ngôn ngữ lập trình nào, bạn sẽ có quyền truy cập vào các triển khai được đánh giá, được vá lỗi thường xuyên của các thuật toán mã hóa được đánh giá cao. Hãy sử dụng chúng! Và đăng ký các danh sách gửi thư/danh sách tư vấn cho công nghệ bạn chọn để đảm bảo bạn nhận thức được các lỗ hổng khi chúng được tìm thấy để bạn có thể giữ chúng được vá và cập nhật.

Đối với việc mã hóa khi nghỉ, trừ khi bạn có một lý do rất tốt để chọn một cái gì đó khác, hãy chọn một triển khai nổi tiếng của **AES-128** hoặc **AES-256** cho nền tảng của bạn.[^1] Cả `runtime` của Java và .NET đều bao gồm các triển khai của AES rất có khả năng đã được kiểm thử kỹ lưỡng (và được vá lỗi tốt), nhưng các thư viện riêng biệt cũng tồn tại cho hầu hết các nền tảng—ví dụ, các thư viện **Bouncy Castle** cho Java và C#.

Đối với mật khẩu, bạn nên xem xét sử dụng một kỹ thuật được gọi là **băm mật khẩu có muối (salted password hashing)**.

Việc mã hóa được triển khai tồi tệ có thể còn tệ hơn là không có gì, vì cảm giác an toàn giả tạo (xin lỗi vì cách chơi chữ) có thể khiến bạn mất cảnh giác.

##### **Tất cả là về các Khóa (It's All About the Keys)**

Như đã đề cập cho đến nay, việc mã hóa dựa vào một thuật toán lấy dữ liệu cần được mã hóa và một **khóa (key)** và sau đó tạo ra dữ liệu đã được mã hóa. Vậy, khóa của bạn được lưu trữ ở đâu? Bây giờ nếu tôi đang mã hóa dữ liệu của mình vì tôi lo lắng về việc ai đó đánh cắp toàn bộ cơ sở dữ liệu của mình, và tôi lưu trữ khóa tôi sử dụng trong cùng một cơ sở dữ liệu, thì tôi thực sự không đạt được nhiều! Do đó, chúng ta cần lưu trữ các khóa ở một nơi khác. Nhưng ở đâu?

Một giải pháp là sử dụng một thiết bị bảo mật riêng biệt để mã hóa và giải mã dữ liệu. Một giải pháp khác là sử dụng một **kho chứa khóa (key vault)** riêng biệt mà dịch vụ của bạn có thể truy cập khi nó cần một khóa. Việc quản lý vòng đời của các khóa (và quyền truy cập để thay đổi chúng) có thể là một hoạt động quan trọng, và các hệ thống này có thể xử lý điều này cho bạn.

Một số cơ sở dữ liệu thậm chí còn bao gồm hỗ trợ tích hợp sẵn cho việc mã hóa, chẳng hạn như Mã hóa Dữ liệu Minh bạch của SQL Server, nhằm mục đích xử lý điều này một cách minh bạch. Ngay cả khi cơ sở dữ liệu bạn chọn có tính năng này, hãy nghiên cứu cách các khóa được xử lý và hiểu liệu mối đe dọa bạn đang bảo vệ chống lại có thực sự được giảm thiểu hay không.

Một lần nữa, những thứ này rất phức tạp. Tránh tự mình triển khai, và hãy nghiên cứu kỹ lưỡng!

##### **Chọn Mục tiêu của bạn (Pick Your Targets)**

Việc giả định mọi thứ nên được mã hóa có thể đơn giản hóa mọi thứ phần nào. Không có sự phỏng đoán về những gì nên hoặc không nên được bảo vệ. Tuy nhiên, bạn vẫn sẽ cần phải suy nghĩ về những dữ liệu nào có thể được đưa vào các tệp nhật ký để giúp xác định vấn đề, và chi phí tính toán của việc mã hóa mọi thứ có thể trở nên khá nặng nề, đòi hỏi phần cứng mạnh hơn. Điều này thậm chí còn thách thức hơn khi bạn đang áp dụng các di chuyển cơ sở dữ liệu như một phần của việc tái cấu trúc các `schema`. Tùy thuộc vào các thay đổi đang được thực hiện, dữ liệu có thể cần phải được giải mã, di chuyển, và mã hóa lại.

Bằng cách chia nhỏ hệ thống của bạn thành các dịch vụ chi tiết hơn, bạn có thể xác định được toàn bộ một kho dữ liệu có thể được mã hóa toàn bộ, nhưng ngay cả khi đó điều đó cũng khó xảy ra. Việc giới hạn việc mã hóa này vào một tập hợp các bảng đã biết là một cách tiếp cận hợp lý.

##### **Giải mã theo Yêu cầu (Decrypt on Demand)**

Mã hóa dữ liệu khi bạn nhìn thấy nó lần đầu tiên. Chỉ giải mã theo yêu cầu, và đảm bảo rằng dữ liệu không bao giờ được lưu trữ ở bất cứ đâu.

##### **Mã hóa các Bản sao lưu (Encrypt Backups)**

Sao lưu là tốt. Chúng ta muốn sao lưu dữ liệu quan trọng của mình, và gần như theo định nghĩa, dữ liệu mà chúng ta đủ lo lắng để muốn mã hóa nó cũng đủ quan trọng để sao lưu! Vì vậy, có vẻ như là một điểm rõ ràng, nhưng chúng ta cần đảm bảo rằng các bản sao lưu của chúng ta cũng được mã hóa. Điều này cũng có nghĩa là chúng ta cần biết những khóa nào cần thiết để xử lý phiên bản dữ liệu nào, đặc biệt nếu các khóa thay đổi. Việc có một hệ thống quản lý khóa rõ ràng trở nên khá quan trọng.

##### **Phòng thủ theo Chiều sâu (Defense in Depth)**

Như tôi đã đề cập trước đó, tôi không thích đặt tất cả trứng của mình vào một giỏ. Tất cả là về phòng thủ theo chiều sâu. Chúng ta đã nói về việc bảo mật dữ liệu khi đang truyền, và bảo mật dữ liệu khi nghỉ. Nhưng có những biện pháp bảo vệ nào khác chúng ta có thể đưa vào để giúp đỡ không?

##### **Tường lửa (Firewalls)**

Việc có một hoặc nhiều tường lửa là một biện pháp phòng ngừa rất hợp lý. Một số rất đơn giản, chỉ có thể hạn chế quyền truy cập vào các loại lưu lượng truy cập nhất định trên các cổng nhất định. Những cái khác phức tạp hơn. Ví dụ, ModSecurity là một loại tường lửa ứng dụng có thể giúp điều tiết các kết nối từ các dải IP nhất định và phát hiện các loại tấn công độc hại khác.

Việc có nhiều hơn một tường lửa là có giá trị. Ví dụ, bạn có thể quyết định sử dụng IPTables cục bộ trên một máy chủ để bảo vệ máy chủ đó, thiết lập các luồng vào và ra được phép. Các quy tắc này có thể được điều chỉnh cho các dịch vụ đang chạy cục bộ, với một tường lửa ở chu vi để kiểm soát truy cập chung.

##### **Ghi nhật ký (Logging)**

Ghi nhật ký tốt, và cụ thể là khả năng tổng hợp nhật ký từ nhiều hệ thống, không phải là về phòng ngừa, nhưng có thể giúp phát hiện và phục hồi sau khi những điều tồi tệ xảy ra. Ví dụ, sau khi áp dụng các bản vá bảo mật, bạn thường có thể thấy trong nhật ký nếu mọi người đã khai thác các lỗ hổng nhất định. Việc vá đảm bảo nó sẽ không xảy ra nữa, nhưng nếu nó đã xảy ra, bạn có thể cần phải vào chế độ phục hồi. Việc có sẵn nhật ký cho phép bạn xem liệu có điều gì đó tồi tệ đã xảy ra sau đó hay không.

Tuy nhiên, lưu ý rằng chúng ta cần phải cẩn thận về những thông tin chúng ta lưu trữ trong nhật ký của mình! Thông tin nhạy cảm cần phải được loại bỏ để đảm bảo chúng ta không rò rỉ dữ liệu quan trọng vào nhật ký của mình, điều này có thể trở thành một mục tiêu tấn công lớn.

##### **Hệ thống Phát hiện (và Ngăn chặn) Xâm nhập (Intrusion Detection (and Prevention) System)**

**Hệ thống phát hiện xâm nhập (Intrusion detection systems - IDS)** có thể giám sát mạng hoặc máy chủ để tìm các hành vi đáng ngờ, báo cáo các vấn đề khi nó thấy chúng. **Hệ thống ngăn chặn xâm nhập (Intrusion prevention systems - IPS)**, cũng như giám sát các hoạt động đáng ngờ, có thể can thiệp để ngăn chặn nó xảy ra. Không giống như một tường lửa, chủ yếu nhìn ra bên ngoài để ngăn chặn những thứ xấu xâm nhập, IDS và IPS đang tích cực tìm kiếm bên trong chu vi để tìm hành vi đáng ngờ. Khi bạn bắt đầu từ đầu, IDS có thể có ý nghĩa nhất. Các hệ thống này dựa trên phỏng đoán (cũng như nhiều tường lửa ứng dụng), và có thể là bộ quy tắc khởi đầu chung sẽ quá khoan dung hoặc không đủ khoan dung cho cách dịch vụ của bạn hoạt động. Việc sử dụng một IDS thụ động hơn để cảnh báo bạn về các vấn đề là một cách tốt để điều chỉnh các quy tắc của bạn trước khi sử dụng nó một cách tích cực hơn.

##### **Phân đoạn Mạng (Network Segregation)**

Với một hệ thống `monolithic`, chúng ta có những giới hạn về cách chúng ta có thể cấu trúc mạng của mình để cung cấp các biện pháp bảo vệ bổ sung. Tuy nhiên, với microservices, bạn có thể đặt chúng vào các phân đoạn mạng khác nhau để kiểm soát thêm cách các dịch vụ nói chuyện với nhau. Ví dụ, AWS cung cấp khả năng tự động cấp phát một **đám mây riêng ảo (virtual private cloud - VPC)**, cho phép các máy chủ sống trong các mạng con riêng biệt. Sau đó, bạn có thể chỉ định VPC nào có thể thấy nhau bằng cách định nghĩa các quy tắc ngang hàng, và thậm chí định tuyến lưu lượng truy cập qua các cổng để ủy quyền truy cập, mang lại cho bạn hiệu quả nhiều chu vi mà tại đó các biện pháp bảo mật bổ sung có thể được đưa vào.

Điều này có thể cho phép bạn phân đoạn các mạng dựa trên quyền sở hữu của đội, hoặc có lẽ theo mức độ rủi ro.

##### **Hệ điều hành (Operating System)**

Các hệ thống của chúng ta dựa vào một lượng lớn phần mềm mà chúng ta không viết, và có thể có các lỗ hổng bảo mật có thể phơi bày ứng dụng của chúng ta, cụ thể là các hệ điều hành của chúng ta và các công cụ hỗ trợ khác chúng ta chạy trên chúng. Ở đây, lời khuyên cơ bản có thể giúp bạn đi một chặng đường dài. Bắt đầu bằng việc chỉ chạy các dịch vụ dưới dạng người dùng HĐH có càng ít quyền càng tốt, để đảm bảo rằng nếu một tài khoản như vậy bị xâm phạm, nó sẽ gây ra thiệt hại tối thiểu.

Tiếp theo, **vá lỗi phần mềm của bạn. Thường xuyên**. Điều này cần phải được tự động hóa, và bạn cần biết liệu các máy của bạn có không đồng bộ với các cấp độ vá lỗi mới nhất hay không. Các công cụ như SCCM của Microsoft hoặc Spacewalk của RedHat có thể có lợi ở đây, vì chúng có thể giúp bạn xem liệu các máy có được cập nhật với các bản vá mới nhất hay không và khởi tạo các bản cập nhật nếu cần. Nếu bạn đang sử dụng các công cụ như Ansible, Puppet, hoặc Chef, rất có thể bạn đã khá hài lòng với việc đẩy ra các thay đổi một cách tự động—những công cụ này cũng có thể giúp bạn đi một chặng đường dài, nhưng sẽ không làm mọi thứ cho bạn.

Đây thực sự là những thứ cơ bản, nhưng thật đáng ngạc nhiên khi tôi thường xuyên thấy phần mềm quan trọng chạy trên các hệ điều hành cũ, chưa được vá lỗi. Bạn có thể có bảo mật cấp ứng dụng được xác định và bảo vệ tốt nhất trên thế giới, nhưng nếu bạn có một phiên bản cũ của một máy chủ web đang chạy trên máy của bạn với quyền `root` có một lỗ hổng tràn bộ đệm chưa được vá, thì hệ thống của bạn vẫn có thể cực kỳ dễ bị tổn thương.

Một điều khác cần xem xét nếu bạn đang sử dụng Linux là sự xuất hiện của các `module` bảo mật cho chính hệ điều hành. Ví dụ, **AppArmor**, cho phép bạn định nghĩa cách ứng dụng của bạn dự kiến sẽ hoạt động, với `kernel` để mắt đến nó. Nếu nó bắt đầu làm điều gì đó không nên, `kernel` sẽ can thiệp. AppArmor đã tồn tại được một thời gian, cũng như SeLinux. Mặc dù về mặt kỹ thuật, một trong hai nên hoạt động trên bất kỳ hệ thống Linux hiện đại nào, trong thực tế, một số bản phân phối hỗ trợ một cái tốt hơn cái kia. Ví dụ, AppArmor được sử dụng theo mặc định trong Ubuntu và SuSE, trong khi SELinux theo truyền thống đã được hỗ trợ tốt bởi RedHat. Một tùy chọn mới hơn là GrSecurity, nhằm mục đích đơn giản hơn để sử dụng so với AppArmor hoặc GrSecurity trong khi cũng cố gắng mở rộng các khả năng của chúng, nhưng nó đòi hỏi một `kernel` tùy chỉnh để hoạt động. Tôi sẽ đề nghị xem xét cả ba để xem cái nào phù hợp nhất với các trường hợp sử dụng của bạn, nhưng tôi thích ý tưởng có một lớp bảo vệ và phòng ngừa khác tại nơi làm việc.

---
#### **Một Ví dụ Thực tế (A Worked Example)**

Việc có một kiến trúc hệ thống chi tiết hơn mang lại cho chúng ta nhiều tự do hơn trong cách chúng ta triển khai bảo mật của mình. Đối với những bộ phận xử lý thông tin nhạy cảm nhất hoặc phơi bày các khả năng có giá trị nhất, chúng ta có thể áp dụng các điều khoản bảo mật nghiêm ngặt nhất. Nhưng đối với các bộ phận khác của hệ thống, chúng ta có thể thoải mái hơn nhiều về những gì chúng ta lo lắng.

Hãy xem xét lại MusicCorp một lần nữa, và kéo một số khái niệm trước đó lại với nhau để xem chúng ta có thể sử dụng một số kỹ thuật bảo mật này ở đâu và như thế nào. Chúng ta đang xem xét chủ yếu các mối quan tâm về bảo mật của dữ liệu khi đang truyền và khi nghỉ. Hình 9-4 cho thấy một tập hợp con của hệ thống tổng thể mà chúng ta sẽ phân tích, hiện đang cho thấy sự thiếu quan tâm đến các mối quan tâm về bảo mật một cách đáng báo động. Mọi thứ đều được gửi qua `HTTP` thuần túy.

![alt text](<images/Screenshot from 2025-09-29 13-12-38.png>)

**Hình 9-4.** *Một tập hợp con của kiến trúc không an toàn đáng tiếc của MusicCorp*

Ở đây chúng ta có các trình duyệt web tiêu chuẩn được khách hàng của chúng ta sử dụng để mua sắm trên trang web. Chúng ta cũng giới thiệu khái niệm về một **cổng bản quyền của bên thứ ba**: chúng ta đã bắt đầu làm việc với một công ty bên thứ ba sẽ xử lý các khoản thanh toán bản quyền cho dịch vụ phát trực tuyến mới của chúng ta. Nó liên hệ với chúng ta thỉnh thoảng để lấy xuống các bản ghi về những gì âm nhạc đã được phát trực tuyến—thông tin mà chúng ta bảo vệ một cách ghen tị vì chúng ta lo lắng về sự cạnh tranh từ các công ty đối thủ. Cuối cùng, chúng ta phơi bày dữ liệu danh mục của mình cho các bên thứ ba khác—ví dụ, cho phép siêu dữ liệu về nghệ sĩ hoặc bài hát được nhúng vào các trang web đánh giá âm nhạc. Bên trong chu vi mạng của chúng ta, chúng ta có một số dịch vụ hợp tác, chỉ được sử dụng nội bộ.

*   Đối với **trình duyệt**, chúng ta sẽ sử dụng một sự kết hợp của lưu lượng `HTTP` tiêu chuẩn cho nội dung không an toàn, để cho phép nó được lưu vào bộ nhớ đệm. Đối với các trang an toàn, đã đăng nhập, tất cả nội dung an toàn sẽ được gửi qua `HTTPS`, mang lại cho khách hàng của chúng ta sự bảo vệ bổ sung nếu họ đang làm những việc như chạy trên các mạng WiFi công cộng.
*   Khi nói đến **hệ thống thanh toán bản quyền của bên thứ ba**, chúng ta không chỉ quan tâm đến bản chất của dữ liệu chúng ta đang phơi bày, mà còn về việc đảm bảo các yêu cầu chúng ta nhận được là hợp pháp. Ở đây, chúng ta khăng khăng rằng bên thứ ba của chúng ta sử dụng **chứng chỉ máy khách**. Tất cả dữ liệu được gửi qua một kênh mật mã an toàn, tăng khả năng của chúng ta để đảm bảo rằng chúng ta đang được yêu cầu dữ liệu này bởi đúng người. Tất nhiên, chúng ta phải suy nghĩ về những gì xảy ra khi dữ liệu rời khỏi sự kiểm soát của chúng ta. Liệu đối tác của chúng ta có quan tâm đến dữ liệu nhiều như chúng ta không?
*   Đối với các **nguồn cấp dữ liệu danh mục**, chúng ta muốn thông tin này được chia sẻ rộng rãi nhất có thể để cho phép mọi người dễ dàng mua nhạc từ chúng ta! Tuy nhiên, chúng ta không muốn điều này bị lạm dụng, và chúng ta muốn có một số ý tưởng về ai đang sử dụng dữ liệu của chúng ta. Ở đây, **khóa API** là hoàn hảo.
*   **Bên trong chu vi mạng**, mọi thứ có một chút tinh tế hơn. Chúng ta lo lắng đến mức nào về việc mọi người xâm phạm mạng nội bộ của chúng ta? Lý tưởng nhất, chúng ta muốn sử dụng `HTTPS` ở mức tối thiểu, nhưng việc quản lý nó có phần đau đớn. Thay vào đó, chúng ta quyết định đặt công việc (ít nhất là ban đầu) vào việc củng cố chu vi mạng của chúng ta, bao gồm việc có một tường lửa được cấu hình đúng cách và chọn một thiết bị bảo mật phần cứng hoặc phần mềm phù hợp để kiểm tra lưu lượng truy cập độc hại (ví dụ: quét cổng hoặc các cuộc tấn công từ chối dịch vụ).
*   Điều đó nói rằng, chúng ta quan tâm đến **một số dữ liệu của chúng ta và nơi nó sống**. Chúng ta không lo lắng về dịch vụ danh mục; rốt cuộc, chúng ta muốn dữ liệu đó được chia sẻ và đã cung cấp một API cho nó! Nhưng chúng ta rất quan tâm đến dữ liệu của khách hàng của mình. Ở đây, chúng ta quyết định mã hóa dữ liệu được giữ bởi dịch vụ khách hàng, và giải mã dữ liệu khi đọc. Nếu kẻ tấn công xâm nhập vào mạng của chúng ta, chúng vẫn có thể chạy các yêu cầu chống lại API của dịch vụ khách hàng, nhưng triển khai hiện tại không cho phép truy xuất hàng loạt dữ liệu khách hàng. Nếu có, chúng ta có thể sẽ xem xét việc sử dụng các chứng chỉ máy khách để bảo vệ thông tin này. Ngay cả khi kẻ tấn công xâm phạm máy mà cơ sở dữ liệu đang chạy và quản lý để tải xuống toàn bộ nội dung, chúng sẽ cần quyền truy cập vào khóa được sử dụng để mã hóa và giải mã dữ liệu để sử dụng nó.

Hình 9-5 cho thấy bức tranh cuối cùng. Như bạn có thể thấy, các lựa chọn chúng ta đã đưa ra về công nghệ nào để sử dụng dựa trên sự hiểu biết về bản chất của thông tin đang được bảo mật. Các mối quan tâm về bảo mật của kiến trúc của riêng bạn có khả năng rất khác nhau, và do đó bạn có thể kết thúc với một giải pháp trông khác.

![alt text](<images/Screenshot from 2025-09-29 13-12-46.png>)

**Hình 9-5.** *Hệ thống an toàn hơn của MusicCorp*

---
[^1]: Nói chung, độ dài khóa càng dài thì càng cần nhiều công sức để phá khóa bằng phương pháp tấn công brute-force. Do đó, bạn có thể giả định rằng khóa càng dài, dữ liệu của bạn càng an toàn. Tuy nhiên, một số lo ngại nhỏ đã được nêu ra về việc triển khai AES-256 cho một số loại khóa nhất định bởi chuyên gia bảo mật uy tín Bruce Schneier. Đây là một trong những lĩnh vực mà bạn cần phải nghiên cứu thêm về những lời khuyên hiện tại tại thời điểm đọc

Chắc chắn rồi! Chúng ta sẽ hoàn thành Chương 9 với các phần cuối cùng, tập trung vào các khía cạnh phi kỹ thuật nhưng cực kỳ quan trọng của bảo mật.

---

#### **Tiết kiệm (Be Frugal)**

Khi không gian đĩa trở nên rẻ hơn và khả năng của các cơ sở dữ liệu được cải thiện, sự dễ dàng mà một lượng lớn thông tin có thể được thu thập và lưu trữ đang tăng lên nhanh chóng. Dữ liệu này có giá trị—không chỉ đối với chính các doanh nghiệp, ngày càng xem dữ liệu như một tài sản có giá trị, mà còn đối với những người dùng coi trọng quyền riêng tư của chính họ. Dữ liệu liên quan đến một cá nhân, hoặc có thể được sử dụng để suy ra thông tin về một cá nhân, phải là dữ liệu mà chúng ta cẩn thận nhất.

Tuy nhiên, điều gì sẽ xảy ra nếu chúng ta làm cho cuộc sống của mình dễ dàng hơn một chút? Tại sao không **xóa bỏ (scrub)** càng nhiều thông tin có thể nhận dạng cá nhân càng tốt, và làm điều đó càng sớm càng tốt? Khi ghi nhật ký một yêu cầu từ một người dùng, chúng ta có cần lưu trữ toàn bộ địa chỉ IP mãi mãi, hay chúng ta có thể thay thế vài chữ số cuối cùng bằng x? Chúng ta có cần lưu trữ tên, tuổi, giới tính và ngày sinh của một người để cung cấp cho cô ấy các ưu đãi sản phẩm, hay chỉ cần phạm vi tuổi và mã bưu điện của cô ấy là đủ thông tin?

Những lợi thế ở đây là rất nhiều.
*   **Thứ nhất**, nếu bạn không lưu trữ nó, không ai có thể đánh cắp nó.
*   **Thứ hai**, nếu bạn không lưu trữ nó, không ai (ví dụ, một cơ quan chính phủ) có thể yêu cầu nó!

Cụm từ tiếng Đức **Datensparsamkeit** đại diện cho khái niệm này. Bắt nguồn từ luật riêng tư của Đức, nó gói gọn khái niệm chỉ lưu trữ nhiều thông tin như là **hoàn toàn cần thiết** để hoàn thành các hoạt động kinh doanh hoặc tuân thủ luật pháp địa phương. Điều này rõ ràng là mâu thuẫn trực tiếp với xu hướng lưu trữ ngày càng nhiều thông tin, nhưng đó là một khởi đầu để nhận ra rằng sự căng thẳng này thậm chí còn tồn tại!

#### **Yếu tố Con người (The Human Element)**

Phần lớn những gì chúng ta đã đề cập ở đây là những điều cơ bản về cách triển khai các biện pháp bảo vệ công nghệ để bảo vệ các hệ thống và dữ liệu của bạn khỏi những kẻ tấn công độc hại, bên ngoài. Tuy nhiên, bạn cũng có thể cần có các quy trình và chính sách để đối phó với yếu tố con người trong tổ chức của mình. Làm thế nào để bạn thu hồi quyền truy cập vào thông tin xác thực khi ai đó rời khỏi tổ chức? Làm thế nào bạn có thể tự bảo vệ mình chống lại **kỹ thuật xã hội (social engineering)**? Như một bài tập tinh thần tốt, hãy xem xét những thiệt hại mà một nhân viên cũ bất mãn có thể gây ra cho các hệ thống của bạn nếu cô ấy muốn. Việc đặt mình vào tư duy của một bên độc hại thường là một cách tốt để suy luận về các biện pháp bảo vệ bạn có thể cần, và ít bên độc hại nào có nhiều thông tin nội bộ như một nhân viên gần đây!

##### **Quy tắc Vàng (The Golden Rule)**

Nếu không có gì khác bạn rút ra từ chương này, hãy để nó là điều này: **đừng viết mật mã của riêng bạn**. Đừng phát minh ra các giao thức bảo mật của riêng bạn. Trừ khi bạn là một chuyên gia mật mã với nhiều năm kinh nghiệm, nếu bạn cố gắng phát minh ra các biện pháp mã hóa hoặc bảo vệ mật mã phức tạp của riêng mình, bạn sẽ làm sai. Và ngay cả khi bạn là một chuyên gia mật mã, bạn vẫn có thể làm sai.

Nhiều công cụ được nêu trước đây, như AES, là các công nghệ đã được ngành công nghiệp tôi luyện, với các thuật toán cơ bản đã được đánh giá ngang hàng, và việc triển khai phần mềm của chúng đã được kiểm thử và vá lỗi nghiêm ngặt trong nhiều năm. Chúng đủ tốt! Việc phát minh lại bánh xe trong nhiều trường hợp thường chỉ là một sự lãng phí thời gian, nhưng khi nói đến bảo mật, nó có thể hoàn toàn nguy hiểm.

##### **Tích hợp Bảo mật ngay từ đầu (Baking Security In)**

Cũng như với kiểm thử chức năng tự động, chúng ta không muốn để bảo mật cho một nhóm người khác, cũng như không muốn để mọi thứ đến phút cuối cùng. Việc giúp giáo dục các nhà phát triển về các mối quan tâm bảo mật là chìa khóa, vì việc nâng cao nhận thức chung của mọi người về các vấn đề bảo mật có thể giúp giảm chúng ngay từ đầu. Việc làm cho mọi người quen thuộc với danh sách Top Ten của OWASP và Khung Kiểm thử Bảo mật của OWASP có thể là một nơi tuyệt vời để bắt đầu. Các chuyên gia hoàn toàn có vị trí của họ, và nếu bạn có quyền truy cập vào họ, hãy sử dụng họ để giúp bạn.

Có các công cụ tự động có thể thăm dò các hệ thống của chúng ta để tìm các lỗ hổng, chẳng hạn như bằng cách tìm kiếm các cuộc tấn công kịch bản chéo trang (`cross-site scripting`). **Zed Attack Proxy (hay ZAP)** là một ví dụ tốt. Được thông báo bởi công việc của OWASP, ZAP cố gắng tái tạo các cuộc tấn công độc hại trên trang web của bạn. Các công cụ khác tồn tại sử dụng phân tích tĩnh để tìm kiếm các lỗi mã hóa phổ biến có thể mở ra các lỗ hổng bảo mật, chẳng hạn như **Brakeman** cho Ruby. Khi các công cụ này có thể dễ dàng được tích hợp vào các bản dựng CI thông thường, hãy tích hợp chúng vào các `check-in` tiêu chuẩn của bạn. Các loại kiểm thử tự động khác phức tạp hơn. Ví dụ, việc sử dụng một cái gì đó như Nessus để quét các lỗ hổng phức tạp hơn một chút và có thể yêu cầu một con người để giải thích kết quả. Điều đó nói rằng, các bài kiểm thử này vẫn có thể tự động hóa được, và có thể hợp lý để chạy chúng với cùng một nhịp điệu như kiểm thử tải.

**Vòng đời Phát triển An toàn (Security Development Lifecycle)** của Microsoft cũng có một số mô hình tốt về cách các đội giao hàng có thể tích hợp bảo mật. Một số khía cạnh của nó có cảm giác quá `waterfall`, nhưng hãy xem xét và xem các khía cạnh nào có thể phù hợp với quy trình làm việc hiện tại của bạn.

##### **Xác minh từ bên ngoài (External Verification)**

Với bảo mật, tôi nghĩ rằng việc có một đánh giá từ bên ngoài là rất có giá trị. Các bài tập như **kiểm thử thâm nhập (penetration testing)**, khi được thực hiện bởi một bên bên ngoài, thực sự bắt chước các nỗ lực trong thế giới thực. Chúng cũng né tránh vấn đề là các đội không phải lúc nào cũng có thể thấy những sai lầm mà họ đã mắc phải, vì họ quá gần gũi với vấn đề. Nếu bạn là một công ty đủ lớn, bạn có thể có một đội infosec chuyên dụng có thể giúp bạn. Nếu không, hãy tìm một bên bên ngoài có thể. Hãy liên hệ với họ sớm, hiểu cách họ thích làm việc, và tìm hiểu họ cần bao nhiêu thông báo để thực hiện một bài kiểm thử.

Bạn cũng sẽ cần xem xét bạn yêu cầu bao nhiêu xác minh trước mỗi bản phát hành. Nói chung, việc thực hiện một bài kiểm thử thâm nhập đầy đủ, ví dụ, không cần thiết cho các bản phát hành tăng dần nhỏ, nhưng có thể cần thiết cho các thay đổi lớn hơn. Những gì bạn cần phụ thuộc vào hồ sơ rủi ro của riêng bạn.

---
#### **Tóm tắt (Summary)**

Vì vậy, một lần nữa chúng ta quay trở lại một chủ đề cốt lõi của cuốn sách—rằng việc có một hệ thống được phân tách thành các dịch vụ chi tiết hơn mang lại cho chúng ta nhiều tùy chọn hơn về cách giải quyết một vấn đề. Không chỉ việc có các microservice có khả năng làm giảm tác động của bất kỳ vi phạm nào, mà nó còn cung cấp cho chúng ta nhiều khả năng hơn để đánh đổi chi phí của các phương pháp phức tạp và an toàn hơn khi dữ liệu nhạy cảm, và một cách tiếp cận nhẹ hơn khi rủi ro thấp hơn.

Một khi bạn hiểu được các mức độ đe dọa của các bộ phận khác nhau trong hệ thống của mình, bạn nên bắt đầu có cảm giác về thời điểm xem xét bảo mật khi đang truyền, khi nghỉ, hoặc hoàn toàn không.

Cuối cùng, hãy hiểu tầm quan trọng của phòng thủ theo chiều sâu, hãy chắc chắn rằng bạn vá lỗi các hệ điều hành của mình, và ngay cả khi bạn coi mình là một ngôi sao nhạc rock, đừng cố gắng tự mình triển khai mật mã!

Nếu bạn muốn có một cái nhìn tổng quan về bảo mật cho các ứng dụng dựa trên trình duyệt, một nơi tuyệt vời để bắt đầu là tổ chức phi lợi nhuận xuất sắc **Open Web Application Security Project (OWASP)**, với tài liệu Top 10 Security Risk được cập nhật thường xuyên nên được coi là tài liệu cần đọc cho bất kỳ nhà phát triển nào. Cuối cùng, nếu bạn muốn có một cuộc thảo luận tổng quát hơn về mật mã, hãy xem cuốn sách *Cryptography Engineering* của Niels Ferguson, Bruce Schneier, và Tadayoshi Kohno (Wiley).

Việc nắm bắt bảo mật thường là về việc hiểu con người và cách họ làm việc với các hệ thống của chúng ta. Một khía cạnh liên quan đến con người mà chúng ta chưa thảo luận về mặt microservices là sự tương tác giữa các cấu trúc tổ chức và chính các kiến trúc. Nhưng cũng như với bảo mật, chúng ta sẽ thấy rằng việc phớt lờ yếu tố con người có thể là một sai lầm nghiêm trọng.

*(Kết thúc Chương 9. Chương tiếp theo, Chương 10, sẽ khám phá "Conway’s Law and System Design".)*


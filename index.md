Kỹ thuật, chiến lược và công thức để xây dựng một __ứng dụng web hiện đại__ với __nhiều team__ có thể __phát hành tính năng một cách độc lập__.

## Micro Frontends là gì? {#what-are-micro-frontends}

Thuật ngữ __Micro Frontends__ xuất hiện lần đầu trên [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar/techniques/micro-frontends) vào cuối năm 2016. Nó mở rộng khái niệm microservice sang thế giới frontend. Xu hướng hiện nay là xây dựng một ứng dụng trình duyệt mạnh mẽ, giàu tính năng, hay còn gọi là single page app (SPA), nằm trên một kiến trúc microservice. Theo thời gian, tầng frontend — thường do một team riêng phát triển — ngày càng phình to và khó bảo trì hơn. Đó là thứ chúng tôi gọi là [Frontend Monolith](https://www.youtube.com/watch?v=pU1gXA0rfwc).

Ý tưởng đằng sau Micro Frontends là coi một website hay ứng dụng web như __một tập hợp các tính năng__ được sở hữu bởi __các team độc lập__. Mỗi team có một __mảng nghiệp vụ__ hoặc __sứ mệnh riêng biệt__ mà họ quan tâm và chuyên sâu. Một team là __đa chức năng (cross functional)__ và phát triển tính năng của mình __từ đầu đến cuối (end-to-end)__, từ cơ sở dữ liệu cho đến giao diện người dùng.

Tuy nhiên, ý tưởng này không mới. Nó có nhiều điểm chung với khái niệm [Self-contained Systems](http://scs-architecture.org/). Trước đây, những cách tiếp cận như vậy được gọi là [Frontend Integration for Verticalised Systems](https://www.otto.de/jobs/en/technology/techblog/blogpost/architecture-principles-2013-04-15.php). Nhưng rõ ràng Micro Frontends là một cái tên thân thiện và gọn gàng hơn.

__Frontend nguyên khối (Monolithic Frontends)__
<img alt="Frontend nguyên khối" src="./ressources/diagrams/organisational/monolith-frontback-microservices.png" loading="lazy" />


__Tổ chức theo chiều dọc (Verticals)__
<img alt="Team end-to-end với Micro Frontends" src="./ressources/diagrams/organisational/verticals-headline.png" loading="lazy" />

## Thế nào là một ứng dụng web hiện đại? {#whats-a-modern-web-app}

Ở phần giới thiệu, tôi đã dùng cụm từ "xây dựng một ứng dụng web hiện đại". Hãy cùng định nghĩa những giả định gắn liền với thuật ngữ này.

Để nhìn ở góc độ rộng hơn, [Aral Balkan](https://ar.al/) đã viết một bài blog về thứ mà anh gọi là [Documents‐to‐Applications Continuum](https://ar.al/notes/the-documents-to-applications-continuum/) (dải liên tục từ tài liệu đến ứng dụng). Anh đưa ra khái niệm về một thang trượt, trong đó một trang web được xây dựng từ __các tài liệu tĩnh__, liên kết với nhau qua đường link, nằm ở __đầu bên trái__, còn một __ứng dụng thuần hành vi, không có nội dung__ như trình chỉnh sửa ảnh trực tuyến thì nằm ở __đầu bên phải__.

Nếu dự án của bạn nằm ở __phía bên trái của dải này__, thì __tích hợp ở tầng web server__ là lựa chọn phù hợp. Với mô hình này, server thu thập và __nối các chuỗi HTML__ từ tất cả các component tạo nên trang mà người dùng yêu cầu. Việc cập nhật được thực hiện bằng cách tải lại trang từ server hoặc thay thế một phần trang qua ajax. [Gustaf Nilsson Kotte](https://twitter.com/gustaf_nk/) đã viết một [bài viết chi tiết](https://gustafnk.github.io/microservice-websites/) về chủ đề này.

Khi giao diện của bạn cần cung cấp __phản hồi tức thì__, kể cả trên kết nối mạng không ổn định, thì một trang chỉ render trên server là không còn đủ nữa. Để triển khai các kỹ thuật như [Optimistic UI](https://www.smashingmagazine.com/2016/11/true-lies-of-optimistic-user-interfaces/) hay [Skeleton Screens](http://www.lukew.com/ff/entry.asp?1797), bạn cần có khả năng __cập nhật__ giao diện __ngay trên thiết bị__. Thuật ngữ [Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/) của Google mô tả rất chính xác __sự cân bằng__ giữa việc trở thành một công dân tốt của web (progressive enhancement) đồng thời mang lại hiệu năng giống như một ứng dụng. Loại ứng dụng này nằm đâu đó __quanh khu vực giữa của dải site-app__. Ở đây, một giải pháp chỉ dựa vào server là không còn đủ. Chúng ta phải chuyển __việc tích hợp vào trình duyệt__, và đó là trọng tâm của bài viết này.

## Các ý tưởng cốt lõi của Micro Frontends {#core-ideas-behind-micro-frontends}

* __Không phụ thuộc công nghệ__<br>Mỗi team nên có thể tự chọn và nâng cấp stack của mình mà không cần phối hợp với các team khác. [Custom Elements](#the-dom-is-the-api) là một cách tuyệt vời để che giấu chi tiết triển khai trong khi vẫn cung cấp một giao diện trung lập cho những team khác.
* __Cô lập code của từng team__<br>Đừng chia sẻ runtime, kể cả khi tất cả các team dùng chung một framework. Hãy xây dựng các ứng dụng độc lập, tự chứa (self contained). Đừng dựa vào state dùng chung hay biến toàn cục.
* __Thiết lập tiền tố cho từng team__<br>Thống nhất quy ước đặt tên ở những chỗ chưa thể cô lập. Đặt namespace cho CSS, Events, Local Storage và Cookies để tránh xung đột và làm rõ quyền sở hữu.
* __Ưu tiên tính năng gốc của trình duyệt thay vì API tự chế__<br>Dùng [Browser Events để giao tiếp](#parent-child-communication--dom-modification) thay vì xây dựng một hệ thống PubSub toàn cục. Nếu thực sự phải xây dựng một API giữa các team, hãy cố giữ nó đơn giản nhất có thể.
* __Xây dựng một trang web bền bỉ (resilient)__<br>Tính năng của bạn nên hữu ích kể cả khi JavaScript bị lỗi hoặc chưa được thực thi. Hãy dùng [Universal Rendering](#server-side-rendering--universal-rendering) và Progressive Enhancement để cải thiện hiệu năng cảm nhận được.

---

## DOM chính là API {#the-dom-is-the-api}

[Custom Elements](https://developers.google.com/web/fundamentals/getting-started/primers/customelements) — khía cạnh về khả năng tương tác trong đặc tả Web Components — là một nền tảng tốt cho việc tích hợp trong trình duyệt. Mỗi team xây dựng component của mình __bằng công nghệ web mà họ chọn__ và __bọc nó trong một Custom Element__ (ví dụ: `<order-minicart></order-minicart>`). Đặc tả DOM của element này (tên thẻ, thuộc tính & sự kiện) đóng vai trò là hợp đồng hay API công khai cho các team khác. Ưu điểm là họ có thể sử dụng component và chức năng của nó mà không cần biết cách triển khai. Họ chỉ cần có khả năng tương tác với DOM.

Nhưng chỉ Custom Elements thôi thì không giải quyết được mọi nhu cầu. Để xử lý progressive enhancement, universal rendering hay routing, chúng ta cần thêm những thành phần phần mềm khác.

Trang này được chia thành hai phần chính. Đầu tiên chúng ta sẽ bàn về [Ghép trang (Page Composition)](#page-composition) — cách lắp ráp một trang từ các component thuộc sở hữu của các team khác nhau. Sau đó chúng tôi sẽ đưa ra ví dụ triển khai [chuyển trang (Page Transition)](#navigating-between-pages) phía client.

## Ghép trang (Page Composition) {#page-composition}

Bên cạnh việc tích hợp __phía client__ và __phía server__ cho code được viết bằng __các framework khác nhau__, còn rất nhiều chủ đề phụ cần được thảo luận: cơ chế __cô lập js__, __tránh xung đột css__, __tải tài nguyên__ khi cần, __chia sẻ tài nguyên chung__ giữa các team, xử lý __lấy dữ liệu (data fetching)__ và suy nghĩ về __trạng thái loading__ tốt cho người dùng. Chúng ta sẽ đi qua từng chủ đề một.

### Bản prototype cơ bản {#the-base-prototype}

Trang sản phẩm của cửa hàng máy kéo mô hình này sẽ là nền tảng cho các ví dụ tiếp theo.

Nó có một __bộ chọn biến thể (variant selector)__ để chuyển đổi giữa ba mẫu máy kéo khác nhau. Khi thay đổi, hình ảnh sản phẩm, tên, giá và các gợi ý sẽ được cập nhật. Ngoài ra còn có một __nút mua__, dùng để thêm biến thể đã chọn vào giỏ hàng, và một __giỏ hàng mini__ ở phía trên được cập nhật tương ứng.

[![Ví dụ 0 - Trang sản phẩm - Plain JS](./ressources/video/model-store-0.gif)](./0-model-store/)

[chạy thử trên trình duyệt](./0-model-store/) & [xem code](https://github.com/neuland/micro-frontends/tree/master/0-model-store)

Toàn bộ HTML được tạo ở phía client bằng __JavaScript thuần__ và ES6 Template Strings, __không có thư viện phụ thuộc__. Code sử dụng cách tách state/markup đơn giản và render lại toàn bộ HTML phía client mỗi khi có thay đổi — không có DOM diffing phức tạp và tạm thời __chưa có universal rendering__. Cũng __chưa tách theo team__ — [toàn bộ code](https://github.com/neuland/micro-frontends/tree/master/0-model-store) được viết trong một file js/css.

### Tích hợp phía client {#client-side-integration}

Trong ví dụ này, trang được chia thành các component/fragment riêng biệt thuộc sở hữu của ba team. __Team Checkout__ (xanh dương) giờ chịu trách nhiệm cho mọi thứ liên quan đến quy trình mua hàng — cụ thể là __nút mua__ và __giỏ hàng mini__. __Team Inspire__ (xanh lá) quản lý phần __gợi ý sản phẩm__ trên trang này. Bản thân trang thuộc sở hữu của __Team Product__ (đỏ).

[![Ví dụ 1 - Trang sản phẩm - Composition](./ressources/screen/three-teams.png)](./1-composition-client-only/)

[chạy thử trên trình duyệt](./1-composition-client-only/) & [xem code](https://github.com/neuland/micro-frontends/tree/master/1-composition-client-only)

__Team Product__ quyết định chức năng nào được đưa vào và đặt ở đâu trong layout. Trang chứa những thông tin mà Team Product có thể tự cung cấp, như tên sản phẩm, hình ảnh và các biến thể có sẵn. Nhưng nó cũng chứa các fragment (Custom Elements) từ các team khác.

### Làm thế nào để tạo một Custom Element? {#how-to-create-a-custom-element}

Hãy lấy __nút mua__ làm ví dụ. Team Product chèn nút này đơn giản bằng cách thêm `<blue-buy sku="t_porsche"></blue-buy>` vào vị trí mong muốn trong markup. Để điều này hoạt động, Team Checkout phải đăng ký element `blue-buy` trên trang.

    class BlueBuy extends HTMLElement {
      connectedCallback() {
        this.attachShadow({ mode: 'open' });
        this.shadowRoot = `<button type="button">buy for 66,00 €</button>`;
      }

      disconnectedCallback() { ... }
    }
    window.customElements.define('blue-buy', BlueBuy);

Giờ đây, mỗi khi trình duyệt gặp một thẻ `blue-buy` mới, `connectedCallback` sẽ được gọi. `this` là tham chiếu tới node DOM gốc của custom element. Mọi thuộc tính và phương thức của một DOM element tiêu chuẩn như `innerHTML` hay `getAttribute()` đều có thể sử dụng.

<img alt="Custom Element hoạt động" src="./ressources/video/custom-element.gif" loading="lazy" />

Khi đặt tên cho element, yêu cầu duy nhất mà đặc tả đưa ra là tên phải __chứa dấu gạch ngang (-)__ để duy trì khả năng tương thích với các thẻ HTML mới trong tương lai. Trong các ví dụ sắp tới, quy ước đặt tên `[màu_team]-[tính_năng]` được sử dụng. Namespace của team giúp tránh xung đột, và theo cách này quyền sở hữu một tính năng trở nên rõ ràng chỉ bằng cách nhìn vào DOM.

### Giao tiếp cha-con / Thay đổi DOM {#parent-child-communication--dom-modification}

Khi người dùng chọn một máy kéo khác trong __bộ chọn biến thể__, __nút mua phải được cập nhật__ tương ứng. Để làm điều này, Team Product có thể đơn giản __xoá__ element hiện có khỏi DOM __và chèn__ một element mới.

    container.innerHTML;
    // => <blue-buy sku="t_porsche">...</blue-buy>
    container.innerHTML = '<blue-buy sku="t_fendt"></blue-buy>';

`disconnectedCallback` của element cũ được gọi đồng bộ để element có cơ hội dọn dẹp những thứ như event listener. Sau đó `connectedCallback` của element `t_fendt` mới được tạo sẽ được gọi.

Một lựa chọn khác hiệu năng tốt hơn là chỉ cập nhật thuộc tính `sku` trên element hiện có.

    document.querySelector('blue-buy').setAttribute('sku', 't_fendt');

Nếu Team Product sử dụng một template engine có DOM diffing, như React, thì việc này sẽ được thuật toán thực hiện tự động.

<img alt="Thay đổi thuộc tính Custom Element" src="./ressources/video/custom-element-attribute.gif" loading="lazy" />

Để hỗ trợ điều này, Custom Element có thể triển khai `attributeChangedCallback` và chỉ định danh sách `observedAttributes` mà callback này sẽ được kích hoạt.

    const prices = {
      t_porsche: '66,00 €',
      t_fendt: '54,00 €',
      t_eicher: '58,00 €',
    };

    class BlueBuy extends HTMLElement {
      static get observedAttributes() {
        return ['sku'];
      }
      connectedCallback() {
        this.render();
      }
      render() {
        if (!this.shadowRoot) {
          this.attachShadow({ mode: 'open' });
        }
        const sku = this.getAttribute('sku');
        const price = prices[sku];
        this.shadowRoot.innerHTML = `<button type="button">buy for ${price}</button>`;
      }
      attributeChangedCallback(attr, oldValue, newValue) {
        this.render();
      }
      disconnectedCallback() {...}
    }
    window.customElements.define('blue-buy', BlueBuy);

Để tránh trùng lặp, một phương thức `render()` được thêm vào và được gọi từ `connectedCallback` và `attributeChangedCallback`. Phương thức này thu thập dữ liệu cần thiết và gán markup mới vào innerHTML. Nếu bạn quyết định dùng một template engine hay framework phức tạp hơn bên trong Custom Element, thì đây là nơi đặt code khởi tạo của nó.

### Hỗ trợ trình duyệt {#browser-support}

Ví dụ trên sử dụng đặc tả Custom Element, vốn [được hỗ trợ bởi tất cả trình duyệt hiện đại](http://caniuse.com/#feat=custom-elementsv1). Không cần polyfill hay hack nào cả.
Điều tương tự cũng đúng với [Shadow DOM](https://caniuse.com/shadowdomv1), được dùng để đóng gói markup và style của Custom Element.

### Tương thích với framework {#framework-compatibility}

Vì Custom Elements là một chuẩn web, tất cả các framework JavaScript lớn như React, Vue, Angular, Svelte hay Preact đều hỗ trợ chúng.
Chúng cho phép bạn nhúng một Custom Element vào ứng dụng giống như một thẻ HTML gốc, đồng thời cung cấp cách để xuất bản ứng dụng viết bằng framework của bạn dưới dạng một Custom Element.

### Tránh hỗn loạn framework {#avoid-framework-anarchy}

Sử dụng Custom Elements là một cách tuyệt vời để đạt được mức độ tách rời (decoupling) cao giữa các fragment của từng team. Bằng cách này, mỗi team được tự do chọn framework frontend mà họ muốn. Nhưng không phải cứ làm được là nên trộn lẫn nhiều công nghệ khác nhau. Hãy cố tránh [Micro Frontends Anarchy](https://www.thoughtworks.com/radar/techniques/micro-frontend-anarchy) và tạo ra mức độ thống nhất hợp lý giữa các team. Nhờ vậy, các team có thể chia sẻ kiến thức và best practice với nhau. Nó cũng giúp bạn dễ dàng hơn khi muốn xây dựng một pattern library chung.
Dù vậy, khả năng kết hợp nhiều công nghệ có thể rất hữu ích khi bạn làm việc với một ứng dụng legacy và muốn chuyển đổi sang một tech stack mới.

### Giao tiếp con-cha hoặc giữa các anh em / DOM Events {#child-parent-or-siblings-communication--dom-events}

Nhưng việc truyền thuộc tính xuống là không đủ cho mọi tương tác. Trong ví dụ của chúng ta, __giỏ hàng mini cần được làm mới__ khi người dùng __click vào nút mua__.

Cả hai fragment đều thuộc sở hữu của Team Checkout (xanh dương), nên họ có thể xây dựng một kiểu API JavaScript nội bộ để giỏ hàng mini biết khi nào nút được bấm. Nhưng điều này đòi hỏi các instance component phải biết về nhau và cũng vi phạm nguyên tắc cô lập.

Một cách gọn gàng hơn là sử dụng cơ chế PubSub, trong đó một component có thể publish một message và các component khác có thể subscribe vào các topic cụ thể. May mắn là trình duyệt đã tích hợp sẵn tính năng này. Đây chính xác là cách các sự kiện trình duyệt như `click`, `select` hay `mouseover` hoạt động. Ngoài các sự kiện gốc, còn có khả năng tạo các sự kiện cấp cao hơn bằng `new CustomEvent(...)`. Sự kiện luôn gắn với node DOM nơi chúng được tạo/dispatch. Hầu hết các sự kiện gốc cũng hỗ trợ bubbling (nổi bọt). Điều này giúp có thể lắng nghe mọi sự kiện trên một cây con cụ thể của DOM. Nếu bạn muốn lắng nghe mọi sự kiện trên trang, hãy gắn event listener vào element window. Dưới đây là cách tạo sự kiện `blue:basket:changed` trong ví dụ:

    class BlueBuy extends HTMLElement {
      [...]
      connectedCallback() {
        [...]
        this.render();
        this.shadowRoot.querySelector('button').addEventListener('click', this.addToCart);
      }
      addToCart() {
        // có thể gọi tới một api
        this.dispatchEvent(new CustomEvent('blue:basket:changed', {
          bubbles: true,
        }));
      }
      render() {
        if (!this.shadowRoot) {
          this.attachShadow({ mode: 'open' });
        }
        this.shadowRoot.innerHTML = `<button type="button">buy</button>`;
      }
      disconnectedCallback() {
        this.shadowRoot.querySelector('button').removeEventListener('click', this.addToCart);
      }
    }

Giờ đây giỏ hàng mini có thể subscribe sự kiện này trên `window` và được thông báo khi cần làm mới dữ liệu.

    class BlueBasket extends HTMLElement {
      connectedCallback() {
        [...]
        window.addEventListener('blue:basket:changed', this.refresh);
      }
      refresh() {
        // lấy dữ liệu mới và render
      }
      disconnectedCallback() {
        window.removeEventListener('blue:basket:changed', this.refresh);
      }
    }

Với cách tiếp cận này, fragment giỏ hàng mini thêm một listener vào một DOM element nằm ngoài phạm vi của nó (`window`). Điều này ổn với nhiều ứng dụng, nhưng nếu bạn không thoải mái với nó, bạn cũng có thể triển khai cách tiếp cận trong đó chính trang (Team Product) lắng nghe sự kiện và thông báo cho giỏ hàng mini bằng cách gọi `refresh()` trên DOM element.

    // page.js
    const $ = document.getElementsByTagName;

    $('blue-buy')[0].addEventListener('blue:basket:changed', function() {
      $('blue-basket')[0].refresh();
    });

Việc gọi các phương thức DOM theo kiểu mệnh lệnh (imperative) khá hiếm gặp, nhưng có thể thấy ví dụ trong [API của video element](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement#methods). Nếu có thể, nên ưu tiên cách tiếp cận khai báo (declarative — thay đổi thuộc tính).

## Render phía server / Universal Rendering {#server-side-rendering--universal-rendering}

Custom Elements rất tuyệt để tích hợp component bên trong trình duyệt. Nhưng khi xây dựng một trang web có thể truy cập trên internet, nhiều khả năng hiệu năng tải lần đầu sẽ quan trọng và người dùng sẽ thấy màn hình trắng cho đến khi tất cả framework js được tải về và thực thi. Ngoài ra, nên suy nghĩ về điều gì xảy ra với trang nếu JavaScript bị lỗi hoặc bị chặn. [Jeremy Keith](https://adactio.com/) giải thích tầm quan trọng của điều này trong ebook/podcast [Resilient Web Design](https://resilientwebdesign.com/) của anh. Vì vậy, khả năng render nội dung cốt lõi trên server là then chốt. Đáng tiếc là đặc tả web component hoàn toàn không đề cập đến việc render phía server. Không có JavaScript, không có Custom Elements :(

### Custom Elements + Server Side Includes = ❤️ {#custom-elements--server-side-includes}

Để render phía server hoạt động, ví dụ trước được tái cấu trúc. Mỗi team có express server riêng và phương thức `render()` của Custom Element cũng có thể truy cập qua url.

    $ curl http://127.0.0.1:3000/blue-buy?sku=t_porsche
    <button type="button">buy for 66,00 €</button>

Tên thẻ của Custom Element được dùng làm đường dẫn — các thuộc tính trở thành query parameter. Giờ đã có cách để render nội dung của mọi component phía server. Kết hợp với Custom Element `<blue-buy>`, ta đạt được một thứ khá gần với __Universal Web Component__:

    <blue-buy sku="t_porsche">
      <!--#include virtual="/blue-buy?sku=t_porsche" -->
    </blue-buy>

Comment `#include` là một phần của [Server Side Includes](https://en.wikipedia.org/wiki/Server_Side_Includes), một tính năng có sẵn trong hầu hết các web server. Vâng, đây chính là kỹ thuật ngày xưa dùng để nhúng ngày hiện tại vào trang web. Cũng có một vài kỹ thuật thay thế như [ESI](https://en.wikipedia.org/wiki/Edge_Side_Includes), [nodesi](https://github.com/Schibsted-Tech-Polska/nodesi), [compoxure](https://github.com/tes/compoxure) và [tailor](https://github.com/zalando/tailor), nhưng với các dự án của chúng tôi, SSI đã chứng tỏ là một giải pháp đơn giản và cực kỳ ổn định.

Comment `#include` được thay thế bằng response của `/blue-buy?sku=t_porsche` trước khi web server gửi toàn bộ trang đến trình duyệt. Cấu hình trong nginx trông như sau:

    upstream team_blue {
      server team_blue:3001;
    }
    upstream team_green {
      server team_green:3002;
    }
    upstream team_red {
      server team_red:3003;
    }

    server {
      listen 3000;
      ssi on;

      location /blue {
        proxy_pass  http://team_blue;
      }
      location /green {
        proxy_pass  http://team_green;
      }
      location /red {
        proxy_pass  http://team_red;
      }
      location / {
        proxy_pass  http://team_red;
      }
    }

Chỉ thị `ssi: on;` bật tính năng SSI, và một block `upstream` cùng `location` được thêm cho mỗi team để đảm bảo mọi url bắt đầu bằng `/blue` sẽ được định tuyến tới đúng ứng dụng (`team_blue:3001`). Ngoài ra, route `/` được ánh xạ tới team đỏ, team kiểm soát trang chủ / trang sản phẩm.

Animation này cho thấy cửa hàng máy kéo trong một trình duyệt đã __tắt JavaScript__.

[![Render phía server - Tắt JavaScript](./ressources/video/server-render.gif)](./ressources/video/server-render.mp4)

[xem code](https://github.com/neuland/micro-frontends/tree/master/2-composition-universal)

Các nút chọn biến thể giờ là các link thực sự và mỗi lần click sẽ tải lại trang. Terminal bên phải minh hoạ quá trình một request cho trang được định tuyến tới team đỏ, team kiểm soát trang sản phẩm, và sau đó markup được bổ sung bởi các fragment từ team xanh dương và xanh lá.

Khi bật lại JavaScript, chỉ có log của server cho request đầu tiên được hiển thị. Mọi thay đổi máy kéo tiếp theo được xử lý phía client, giống như ở ví dụ đầu tiên. Trong một ví dụ sau, dữ liệu sản phẩm sẽ được tách khỏi JavaScript và tải qua REST api khi cần.

Bạn có thể chạy thử code mẫu này trên máy của mình. Chỉ cần cài đặt [Docker Compose](https://docs.docker.com/compose/install/).

    git clone https://github.com/neuland/micro-frontends.git
    cd micro-frontends/2-composition-universal
    docker-compose up --build

Docker sẽ khởi động nginx trên cổng 3000 và build image node.js cho mỗi team. Khi bạn mở [http://127.0.0.1:3000/](http://127.0.0.1:3000/) trên trình duyệt, bạn sẽ thấy một chiếc máy kéo màu đỏ. Log tổng hợp của `docker-compose` giúp dễ dàng theo dõi những gì đang diễn ra trong mạng. Tiếc là không có cách nào kiểm soát màu sắc output, nên bạn phải chịu đựng việc team xanh dương có thể bị tô màu xanh lá :)

Các file `src` được ánh xạ vào từng container và ứng dụng node sẽ khởi động lại khi bạn thay đổi code. Thay đổi `nginx.conf` thì cần khởi động lại `docker-compose` mới có hiệu lực. Vì vậy hãy thoải mái vọc và gửi phản hồi.

### Lấy dữ liệu & Trạng thái loading {#data-fetching--loading-states}

Một nhược điểm của cách tiếp cận SSI/ESI là __fragment chậm nhất sẽ quyết định thời gian phản hồi__ của toàn bộ trang.
Vì vậy sẽ tốt nếu response của một fragment có thể được cache.
Với những fragment tốn kém để tạo ra và khó cache, thường nên loại chúng khỏi lần render đầu tiên.
Chúng có thể được tải bất đồng bộ trên trình duyệt.
Trong ví dụ của chúng ta, fragment `green-recos` hiển thị gợi ý cá nhân hoá là một ứng viên cho việc này.

Một giải pháp khả thi là team đỏ chỉ cần bỏ qua SSI Include.

**Trước**

    <green-recos sku="t_porsche">
      <!--#include virtual="/green-recos?sku=t_porsche" -->
    </green-recos>

**Sau**

    <green-recos sku="t_porsche"></green-recos>

*Lưu ý quan trọng: Custom Elements [không thể tự đóng (self-closing)](https://developers.google.com/web/fundamentals/web-components/customelements), nên viết `<green-recos sku="t_porsche" />` sẽ không hoạt động đúng.*

<img alt="Reflow" src="./ressources/video/data-fetching-reflow.gif" style="width: 500px" loading="lazy" />

Việc render chỉ diễn ra trên trình duyệt.
Nhưng như có thể thấy trong animation, thay đổi này đã gây ra __reflow đáng kể__ cho trang.
Khu vực gợi ý ban đầu trống trơn.
JavaScript của team xanh lá được tải và thực thi.
API call để lấy gợi ý cá nhân hoá được thực hiện.
Markup gợi ý được render và các hình ảnh liên quan được request.
Fragment giờ cần nhiều không gian hơn và đẩy layout của trang.

Có nhiều cách khác nhau để tránh một reflow khó chịu như vậy.
Team đỏ, team kiểm soát trang, có thể __cố định chiều cao của container gợi ý__.
Trên một trang web responsive, thường khó xác định chiều cao vì nó có thể khác nhau tuỳ kích thước màn hình.
Nhưng vấn đề quan trọng hơn là __kiểu thoả thuận giữa các team này tạo ra sự phụ thuộc chặt chẽ (tight coupling)__ giữa team đỏ và xanh lá.
Nếu team xanh lá muốn thêm một tiêu đề phụ vào element gợi ý, họ sẽ phải phối hợp với team đỏ về chiều cao mới.
Cả hai team sẽ phải triển khai thay đổi cùng lúc để tránh vỡ layout.

Một cách tốt hơn là sử dụng kỹ thuật gọi là [Skeleton Screens](https://blog.prototypr.io/luke-wroblewski-introduced-skeleton-screens-in-2013-through-his-work-on-the-polar-app-later-fd1d32a6a8e7).
Team đỏ giữ nguyên SSI Include của `green-recos` trong markup.
Ngoài ra, team xanh lá thay đổi __phương thức render phía server__ của fragment để nó tạo ra __một phiên bản phác thảo của nội dung__.
__Skeleton markup__ có thể tái sử dụng một phần style layout của nội dung thật.
Bằng cách này nó __giữ trước không gian cần thiết__ và việc điền nội dung thực tế sẽ không gây ra hiện tượng nhảy layout.

<img alt="Skeleton Screen" src="./ressources/video/data-fetching-skeleton.gif" style="width: 500px" loading="lazy" />

Skeleton screen cũng __rất hữu ích cho render phía client__.
Khi custom element của bạn được chèn vào DOM do hành động của người dùng, nó có thể __render skeleton ngay lập tức__ cho đến khi dữ liệu cần thiết từ server về tới.

Ngay cả khi __thay đổi thuộc tính__ như với _bộ chọn biến thể_, bạn cũng có thể quyết định chuyển sang chế độ skeleton cho đến khi dữ liệu mới về.
Bằng cách này người dùng nhận được tín hiệu rằng có điều gì đó đang diễn ra trong fragment.
Nhưng khi endpoint của bạn phản hồi nhanh, một __cú nháy skeleton__ ngắn giữa dữ liệu cũ và mới cũng có thể gây khó chịu.
Giữ lại dữ liệu cũ hoặc sử dụng timeout thông minh có thể giúp ích.
Vì vậy hãy dùng kỹ thuật này một cách khôn ngoan và cố gắng thu thập phản hồi từ người dùng.

## Cần thêm ví dụ? {#need-more-examples}


<a href="https://micro-frontends.org/tractor-store/">
<img alt="Tractor Store 2.0" src="./ressources/video/tractor-store-2.webp" style="width: 500px" loading="lazy" />
</a>

[Khám phá Tractor Store 2.0](https://micro-frontends.org/tractor-store/)

Đây là một ví dụ micro frontends sát với thực tế hơn, bao gồm các thách thức như __routing & điều hướng__, __giao tiếp__, __quản lý state__, __chiến lược tải__, __chia sẻ code__, __tối ưu tài nguyên__ và __testing__.
Trang này là tập hợp các bản triển khai bằng những công nghệ khác nhau cho cùng một ứng dụng.
Hãy coi nó như [TodoMVC](http://todomvc.com/) dành cho micro frontends.



## Tài liệu tham khảo thêm {#additional-resources}
- [Sách: Micro Frontends in Action](https://www.manning.com/books/micro-frontends-in-action?a_aid=mfia&a_bid=5f09fdeb) Do chính tôi viết.
- [Talk: Micro Frontends - MicroCPH, Copenhagen 2019](https://www.youtube.com/watch?v=wCHYILvM7kU) ([Slides](https://noti.st/naltatis/zQb2m5/micro-frontends-the-nitty-gritty-details-or-frontend-backend-happyend)) The Nitty Gritty Details or Frontend, Backend, 🌈 Happyend
- [Talk: Micro Frontends - Web Rebels, Oslo 2018](https://www.youtube.com/watch?v=dTW7eJsIHDg) ([Slides](https://noti.st/naltatis/HxcUfZ/micro-frontends-think-smaller-avoid-the-monolith-love-the-backend)) Think Smaller, Avoid the Monolith, ❤️the Backend
- [Slides: Micro Frontends - JSUnconf.eu 2017](https://speakerdeck.com/naltatis/micro-frontends-building-a-modern-webapp-with-multiple-teams)
- [Talk: Break Up With Your Frontend Monolith - JS Kongress 2017](https://www.youtube.com/watch?v=W3_8sxUurzA) Elisabeth Engel nói về việc triển khai Micro Frontends tại gutefrage.net
- [Bài viết: Micro Frontends](https://martinfowler.com/articles/micro-frontends.html) Bài viết của Cam Jackson trên blog của Martin Fowler
- [Bài viết: Micro frontends - a microservice approach to front-end web development](https://medium.com/@tomsoderlund/micro-frontends-a-microservice-approach-to-front-end-web-development-f325ebdadc16) Tom Söderlund giải thích khái niệm cốt lõi và cung cấp các link về chủ đề này
- [Bài viết: Microservices to Micro-Frontends](http://www.agilechamps.com/microservices-to-micro-frontends/) Sandeep Jain tóm tắt các nguyên tắc chính đằng sau microservices và micro frontends
- [Tổng hợp link: Micro Frontends by Elisabeth Engel](https://micro-frontends.zeef.com/elisabeth.engel?ref=elisabeth.engel&share=ee53d51a914b4951ae5c94ece97642fc) danh sách phong phú các bài viết, talk, công cụ và tài nguyên khác về chủ đề này
- [Awesome Micro Frontends](https://github.com/ChristianUlbrich/awesome-microfrontends) danh sách link được tuyển chọn bởi Christian Ulbrich 🕶
- [Custom Elements Everywhere](https://custom-elements-everywhere.com/) Đảm bảo các framework và custom elements có thể làm bạn thân của nhau
- Máy kéo có thể mua tại [manufactum.com](https://www.manufactum.com/) :)<br>_Cửa hàng này được phát triển bởi hai team sử dụng các kỹ thuật được mô tả ở đây._


## Người đóng góp {#contributors}
- [Koike Takayuki](https://github.com/koiketakayuki) dịch trang sang [tiếng Nhật](https://micro-frontends-japanese.org/).
- [Jorge Beltrán](https://github.com/scipion) dịch trang sang [tiếng Tây Ban Nha](https://micro-frontends-es.org).
- [Bruno Carneiro](https://github.com/Tautorn) dịch trang sang [tiếng Bồ Đào Nha](https://tautorn.github.io/micro-frontends/).
- [Soobin Bak](https://github.com/soobing) dịch trang sang [tiếng Hàn](https://soobing.github.io/micro-frontends/).
- [Sergei Babin](https://github.com/serzn1) dịch trang sang [tiếng Nga](https://serzn1.github.io/micro-frontends/).
- [Shiwei Yang](https://github.com/swearer23) dịch trang sang [tiếng Trung](https://swearer23.github.io/micro-frontends/).
- [Riccardo Moschetti](https://github.com/RiccardoGMoschetti) dịch trang sang [tiếng Ý](https://riccardogmoschetti.github.io/micro-frontends/).
- [Dominik Czechowski](https://github.com/dominikcz) dịch trang sang [tiếng Ba Lan](https://dominikcz.github.io/micro-frontends/).
- [John Adib](https://github.com/johnadib) dịch trang sang [tiếng Ba Tư](https://johnadib.github.io/micro-frontends/).
- [Mehmet Mutlu](https://github.com/mhmtmutlu) dịch trang sang [tiếng Thổ Nhĩ Kỳ](https://mhmtmutlu.github.io/micro-frontends/).


Trang này được tạo bởi Github Pages. Bản gốc tiếng Anh có tại [micro-frontends.org](https://micro-frontends.org/), mã nguồn tại [neuland/micro-frontends](https://github.com/neuland/micro-frontends/).

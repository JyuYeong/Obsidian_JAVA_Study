# Swing 사용
## JFrame
### JFrame의 주요 메서드
- `setBounds(int x, int y, int width, int height) : void`
	- 창<font color="#7f7f7f">(JFrame)</font>의 위치와 크기를 정한다.
- `setLocatio(int x, int y) : void`
	- 창<font color="#7f7f7f">(JFrame)</font>의 위치를 정한다.
- `pack() : void`
	- 창<font color="#7f7f7f">(JFrame)</font>의 내용물 크기에 맞게 프레임의 크기를 조절
- `setResizable(boolean resizable) : void`
	- 사용자에게 프레임의 크기 조절을 허용하거나 금지
- `setVisible(boolean visible) : void`
	- 프레임을 표시하거나 숨기도록 설정
### 레이아웃 Layout
#### BorderLayout
- 동, 서, 남, 북, 중앙의 다섯 가지 위치로 배치하는 레이아웃 매니저
- 각 위치에는 하나의 컴포넌트만 배치할 수 있다.
##### BorderLayout 생성자 오버로드
- `BorderLayout()`
	- 동서남북 및 중앙 위치에 컴포넌트를 배치하는 기본 생성자
- `BorderLayout(int hgap, int vgap)`
	- 수평 및 수직 간격을 설정할 수 있는 생성자
##### BorderLayout 주요 메서드
- `addLayoutComponent(Component comp, Object constraints) : void`
	- 컴포넌트를 추가하고 배치 제약 조건을 설정한다.
- `addLayoutComponent(String name, Component comp) : void`
	- 컴포넌트를 추가하고 배치 제약 조건을 이름으로 설정한다.
- `removeLayoutComponent(Compoenent comp) : void`
	- 컴포넌트를 제거한다.
- `layoutContainer(Container parent) : void`
	- 컨테이너의 컴포넌트를 배치한다.
#### FlowLayout
- 컴포넌트를 한 행에 나열하되, 공간이 부족하면 다음 행으로 이동하는 레이아웃 매니저
##### FlowLayout 생성자 오버로드
- `FlowLayout()`
	- 컴포넌트들을 왼쪽에서 오른쪽으로 배치하는 기본 생성자
- `FlowLayout(int align)`
	- 정렬 방향을 설정할 수 있는 생성자.
	- `align`으로는 `Flowlayout.LEFT`, `FlowLayout.CENTER`, `FlowLayout.RIGHT` 중 하나를 전달한다.
- `FlowLayout(int align, int hgap, int vgap)`
	- 정렬 방향과 수평 및 수직 간격을 설정할 수 있는 생성자
#### GridLayout
- 테이블 형태로 컴포넌트를 배치하는 레이아웃 매니저
- 지정되 행과 열의 그리드로 컴포넌트를 정렬한다.
##### GridLayout 생성자 오버로드
- `GridLayout()`
	- 인자 없는 생성자로, 행과 열이 없는 기본 그리드를 생성한다.
	- 컴포넌트가 추가되면 기본적으로 1x1 그리드에서 시작한다.
- `GridLayout(int rows, int cols)`
	- 행과 열의 수를 인자로 받아 그 크기로 초기화된 그리드를 생성한다.
- `GridLayout(int rows, int cols, int hgap, int vgap)`
	- 행과 열의 수뿐만 아니라 수평 및 수직 갭을 인자로 받아 초기화된 그리드를 생성한다.
	- 갭은 컴포넌트 사이의 간격을 나타낸다.
### Dimension 객체와의 차이
- Dimension 객체를 사용하면 창의 너비와 높이를 변수로 저장하므로 추후 코드에서 참조하기가 더 쉬워진다.
## JPanel
- 다른 컴포넌트를 그룹화하거나 복합 컴포넌트를 만들 때 사용된다.
- 주로 다른 Swing 컴포넌트들을 담을 수 있는 컨테이너로 활용되며, 일종의 판넬이라고 생각할 수 있다.
### JPanel 생성자 오버로드
- `JPanel()`
- `JPanel(LayoutManager layout)`
### JPanel 주요 메서드
- `add(Component comp) : void`
	- 컴포넌트를 패널에 추가한다.
- `remove(Component comp) : void`
	- 패널에서 컴포넌트를 제거한다.
- `setLayout(LayoutManager layout) : void`
	- 레이아웃 매니저를 설정한다.
- `setBackground(Color bg) : void`
	- 패널의 배경 색상을 설정한다.
- `setPreferredSize(Dimension preferredSize) : void`
	- JPanel의 크기를 명시적으로 설정하는 데 사용된다.
	- Dimension 객체를 매개변수로 받아 <font color="#92cddc">해당 차원을 사용하여 패널의 선호 크기를 지정</font>한다. <font color="#c3d69b">Dimension 객체는 너비와 높이를 나타내는 두 개의 정수 값을 가지므로</font> 패널에 원하는 크기를 설정할 수 있다.
## JLabel
- 텍스트나 이미지를 표시할 수 있는 레이블을 나타낸다.
- 주료 사용자에게 정보를 제공하거나 설명을 표시하는 데 사용된다.
### JLabel 생성자 오버로드
- `JLabel()`
	- 빈 JLabel 생성
- `JLabel(String text)`
	- 주어진 텍스트로 JLabel 생성
- `JLabel(Icon image)`
	- 주어진 이미지로 JLabel 생성
- `JLabel(String text, int horizontalAlignment)`
	- 주어진 텍스트와 수평 정렬 상태로 JLabel 생성
### JLabel 주요 메서드
- `setText(String text) : void`
	- 레이블의 텍스트를 설정
- `setIcon(Icon icon) : void`
	- 레이블의 아이콘을 설정
- `setHorizontalAlignment(int alignment) : void`
	- 수평 정렬을 설정
- `setVerticalAlignment(int alignment) : void`
	- 수직 정렬을 설정
- `setFont(Font font) : void`
	- 레이블의 폰트를 설정
### Layout
![[GUI 프로그래밍#레이아웃 Layout]]
## JScrollPane
### JScrollPane 생성자 오버로드
- `JScrollPane()`
- `JScrollPane(Component view)`
	- 설명 : 주어진 컴포넌트를 보여줄 스크롤 패널을 생성한다. 주어진 컴포넌트는 스크롤 가능한 뷰 영역으로 사용된다.
- `JScrollPane(Component view, int vsbPolicy, int hsbPolicy)`
- `JScrollPane(int vsbPolicy, int hsbPolicy)`
### JScrollPane 사용 방법
- 사용 : JTextArea -> <font color="#92cddc">JScrollPane</font> -> JFrame
## JButton
### JButton 생성자 오버로드
- `JButton()`
- `JButton(String text)`
	- 설명 : 주어진 텍스트를 레이블로 가지는 버튼 생성
- `JButton(Icon icon)`
- `JButton(String text, Icon icon)`
## JTextField
### JTextField 생성자 오버로드
- `JTextField()`
- `JTextField(String text)`
	- 설명 : 주어진 초기 텍스트를 가지는 텍스트 필드 생성
- `JTextField(int columns)`
	- 주어진 열 수<font color="#7f7f7f">(field 넓이)</font>로 초기화된 텍스트 필드 생성
- `JTextField(String text, int columns)`
### JTextField 주요 메서드
- `setText(String text) : void`
	- 텍스트 필드의 텍스트를 설정한다.
- `getText() : String`
	- 텍스트 필드의 현재 텍스트를 반환한다.
- `addActionListener(ActionListener l) : void`
	- ActionListener를 등록하여 엔터 키나 포커스가 변경될 때 알림을 받는다.
- `setEditable(boolean b) : void`
	- 텍스트 필드의 편집 가능 여부를 설정한다.
- `selectAll() : void`
	- 텍스트 필드의 모든 텍스트를 선택한다.
- `setCaretPosition(int position) : void`
	- 캐럿의 위치 설정
- `setSelectionStart(int start) : void`
	- 선택 영역의 시작 위치를 설정
- `setSelectionEnd(int end) : void`
	- 선택 영역의 끝 위치를 설정
## JTextArea
### JTextArea 생성자 오버로드
- `JTextArea()`
- `JTextArea(String text)`
	- 설명 주어진 초기 텍스트를 가지는 텍스트 영역을 생성
- `JTextArea(int rows, int columns)`
	- 설명 : 주어진 행 수와 열 수로 초기화된 텍스트 영역을 생성
- `JTextArea(String text, int rows, int columns)`
### JTextArea 사용 방법
- 사용 : <font color="#92cddc">JTextArea</font> -> JFrame
- 사용 : <font color="#92cddc">JTextArea</font> -> JScrollPane -> JFrame
	- 스크롤을 추가하여 프레임에 등록하는 코스
### JTextArea 주요 메서드
- `setText(String text) : void`
	- 텍스트 영역에 표시할 문자열을 설정한다.
- `append(String str) : void`
	- 주어진 문자열을 JTextArea의 현재 텍스트 끝에 추가한다.
- `setEditable(boolean b) : void`
	- 컴포넌트 편집 가능 여부를 설정
## JMenuBar
### JMenuBar 생성자
- `JMenuBar()`
### JMenuBar 사용 방법
- 사용 : JMenu -> <font color="#92cddc">JMenuBar</font> -> JFrame
	- JFrame에 등록할 때 `setJMenuBar(JMenuBar menubar)`를 사용한다.
## JMenu
### JMenu 생성자 오버로드
- `JMenu(String text)`
- `JMenu(String text, boolean b)`
	- `b` : JMenu가 플래틀<font color="#7f7f7f">(menu bar에 표시되지 않는)</font>인지 여부
### JMenu 사용 방법
### JMenu 메서드
- addSeparator() : void
	- 구분선
## JMenuItem
### JMenuItem 생성자 오버로드
- `JMenuItem(String text)`
	- 주어진 텍스트를 가진 JMenuItem 생성
- `JMenuItem(String text, int mnemonic)`
	- `mnemonic` : 메뉴 항목에 대한 단축키 키 코드
- `JMenuItem(String text, Icon icon)`
- `JMenuItem(String text, int mnemonic, Icon icon)`
- `JMenuItem(Action a)`
	- 주어진 액션<font color="#7f7f7f">(`Action` 객체)</font>을 기반으로 새로운 JMenuItem을 생성
- `JMenuItem(Icon icon)`
	- 주어진 아이콘을 가진 메뉴 항목 생
### JMenuItem 사용 방법
- 사용 : <font color="#92cddc">JMenuItem</font> -> JMenu
### JMenuItem 메서드
## JTable
- 테이블 형태의 데이터를 표시하는 데 사용된다.
- 스프레드시트와 같은 표 형식으로 데이터를 표시하고 편집하는 데 유용하다.
### JTable 사용 절차
1. 데이터 모델 생성
	- JTable에 표시될 데이터를 담고 있는 모델을 생성한다.
	- `DefaultTableModel` 또는 사용자 정의 모델을 구현한다.
2. JTable 생성
3. 헤더 생성
	- 테이블의 열 및 행 헤더를 설정한다.
4. 스크롤 패널 생성
5. 프레임에 추가
- 예제 코드
	```java
	// 데이터 모델 생성
	DefaultTableModel model = new DefaultTableModel();
	model.addColumn("이름");
	model.addColumn("나이");
	model.addRow(new Object[]{"John", 25});
	model.addRow(new Object[]{"Jane", 30});
	
	// JTable 생성 및 데이터 모델 설정
	JTable table = new JTable(model);
	
	// 헤더 생성
	JScrollPane scrollPane = new JScrollPane(table);
	
	// 프레임에 추가
	JFrame frame = new JFrame("JTable 예제");
	frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	frame.getContentPane().add(scrollPane);
	frame.pack();
	frame.setVisible(true);
	```
- 참고 로직
	![[JTable 사용 절차.canvas|JTable 사용 절차]]
### JTable 생성자 오버로드
- `JTable()`
	- 빈 JTable 생성
- `JTable(TableModel dm)`
	- 지정된 데이터 모델을 사용하여 JTable 생성
- `JTable(Object[][] rowData, Object[] columnNames)`
	- 주어진 2D 배열의 데이터와 열 이름 배열을 사용하여 JTable 생성
### JTable 주요 메서드
- `setModel(TableModel dataModel)`
	- JTable에 새로운 데이터 모델을 설정
- `getSelectedRow()`
	- 선택된 행의 인덱스를 반환
- `getSelectedColumn()`
	- 선택된 열의 인덱스를 반환
- `getValueAt(int row, int column)`
	- 주어진 행과 열의 셀 값 반환
- `getValueAt(Object value, int row, int column)`
	- 주어진 행과 열의 셀 값을 설정
- `addMouseListener(MouseListener listener)`
	- 마우스 이벤트를 처리하는 리스너를 추가
## DefaultTableModel
- `JTable`과 함께 사용되며 표 형식의 데이터를 관리하고 표시하는 데 유용하다.
- 주로 테이블의 데이터를 동적으로 변경하고 업데이트하는 데 적합하다.
### DefaulTableModel 생성자 오버로드
- `DefaultTableModel()`
	- 빈 DefaultTableModel 생성
- `DefaultTableModel(Object[] columnNames, int rowCount)`
	- 주어진 열 이름 배열과 행 수로 DefaultTableModel 생성
- `DefaultTableModel(Vector<?> dataVector, Vector<?> columnNames)`
	- 주어진 데이터 벡터와 열 이름 벡터로 DefaultTableModel 생성
### DefaultTableModel 주요 메서드
- `addColumn(Object columnName)`
	- 새로운 열 추가
- `addRow(Object[] rowData)`
	- 새로운 행 추가
- `removeRow(int row)`
	- 주어진 행 제거
- `getDataVector()`
	- 데이터 벡터 반환
- `getColumnCount()`
	- 열의 수 반환
- `getRowCount()`
	- 행의 수 반환
- `setValueAt(Object value, int row, int column)`
	- 주어진 행과 열의 셀 값을 설정
- `getColumnClass(int columnIndex)`
	- 주어진 열의 데이터 형식을 반환
## JFileChooser
- 파일 및 디렉토리를 선택하고 열거나 저장하는 데 사용되는 그래픽 사용자 인터페이스 컴포넌트이다.
### JFileChooser 생성자 오버로드
- `JFileChooser()`
- `JFileChooser(File currentDirectory)`
	- 주어진 기본 디렉토리를 사용하여 파일 선택 대화 상자를 생성
- `JFileChooser(String currentDirectoryPath)`
	- 주어진 기본 디렉토리 경로를 사용하여 파일 선택 대화 상자를 생성
- `JFileChooser(File currentDirectory, FileFilter filter)`
	- 주어진 기본 디렉토리와 파일 필터를 사용하여 파일 선택 대화 상자를 생성
- `JFileChooser(String currentDirectoryPath, FileFilter filter)`
	- 주어진 기본 디렉토리 경로와 파일 필터를 사용하여 파일 선택 대화 상자를 생
### JFileChooser 사용 방법
- 사용 : <font color="#92cddc">JFileChooser</font> -> JFileChooser.APPROVE_OPTION : int
	- APPROVE_OPTION 이외에도 다양한 옵션이 있다.
### JFileChooser 메서드
- `showOpenDialog(Component Parent) : int`
	- 파일 열기 창 띄우기, 옵션값<font color="#7f7f7f">(int)</font> 반환
		- CANCEL_OPTION
		- APPROVE_OPTION
		- ERROR_OPTION
	- `parent`
		- 파일 대화 상자를 표시할 부모 컴포넌트를 지정한다.
- `showSaveDialog(Component parent) : int`
	- 파일 저장 대화 상자를 표시하고 파일 대화 상자가 닫힐 때 사용자의 작업 결과를 나타내는 정수 값을 반환한다.
	- `parent`
		- 파일 대화 상자를 표시할 부모 컴포넌트를 지정한다.
- `getSelectedFile() : File`
	- 사용자가 선택한 파일을 나타내는 `File` 객체를 반환한다.
- `getSelectedFiles() : File[]`
	- 사용자가 선택한 파일 목록을 나타내는 `File` 배열 반환한다.
- `setFileSelectionMode(int mode) : void`
	- 파일 선택 모드를 설정하는 메서드
- `setChoosableFileFilter(FileFilter filter) : void`
	- 사용자 지정 파일 필터를 추가하는 메서드
- `setAcceptAllFileFilterUsed(boolean b) : void`
	- 모든 파일 필터를 사용할지 여부를 설정하는 메서드
- `setCurrentDirectory(File dir) : void`
	- 현재 디렉토리를 설정하는 메서드
## JDialog
- 모달^[사용자 인터페이스에서 특정 창이나 대화 상자가 나타났을 때, 해당 창이나 대화 상자 외의 부분에 대한 상호 작용이 차단되는 것을 의미한다.] 또는 모달리스 다이얼로그를 나타내는 Swing 컴포넌트
- 다이얼로그란 사용자에게 정보를 요청하거나 메시지를 전달하기 위해 일시적으로 나타나는 작은 창이다.
### JDialog 생성자 오버로드
- `JDialog()`
	- 빈 다이얼로그를 생성
- `JDialog(Frame owner)`
	- 지정되 소유자 프레임을 가지는 다이얼로그를 생성
- `JDialog(Frame owner, boolean modal)`
	- 지정된 소유자 프레임과 모달 여부를 가지는 다이얼로그를 생성
- `JDialog(Frame owner, String title)`
	- 지정된 소유자 프레임과 제목을 가지는 다이얼로그를 생성
- `JDialog(Frame owner, String title, boolean modal)`
	- 지정된 소유자 프레임, 제목, 모달 여부를 가지는 다이얼로그를 생성
### JDialog 주요 메서드
- `setDefaultCloseOperation(int operation) : void`
	- 다이얼로그가 닫힐 때의 동작 설정
- `setTitle(String title) : void`
	- 다이얼로그의 제목 설정
- `setModal(boolean modal) : void`
	- 다이얼로그가 모달인지 아닌지를 설정
- `setContentPane(Container contentPane) : void`
	- 다이얼로그의 컨텐츠 팬을 설정
- `setVisible(boolean b) : void`
	- 다이얼로그의 가시성을 설정
- `pack() : void`
	- 다이얼로그의 크기와 위치를 설정
- `setLocationRelativeTo(Component c) : void`
	- 다이얼로그를 지정된 컴포넌트의 상대적인 위치에 표시
## JOptionPane : static
- 대화 상자<font color="#7f7f7f">(dialog box)</font>를 표시하기 위한 클래스
- 간단한 메시지 상자, 입력 대화 상자, 선택 상자 및 사용자 정의 컴포넌트를 표시할 수 있다.
- 정적 메서드를 사용하므로 단순한 메시지를 표시하거나 간단한 입력을 받는 등의 작업에 대해서는 생성자를 호출할 필요가 없다. 즉, JOptionPane을 그대로 사용한다.
### JOptionPane 주요 메서드
- `static showConfirmDialog(Component parencComponent, Object message, String title, int optionType, int messageType)`
	- `parentComponent`
		- 대화 상자를 표시할 부모 컴포넌트를 지정한다. 주로 `null` 또는 부모 창의 컴포넌트를 사용한다.
	- `message`
		- 사용자에게 표시할 메시지나 내용을 포함하는 `Object`이다. 이는 문자열 또는 컴포넌트가 될 수 있다.
	- `title`
		- 대화 상자의 제목을 나타내는 문자열이다.
	- `optionType`
		- 대화 상자에 표시할 버튼 옵션 유형이다. `JOptionPane.YES_NO_OPTION` 등이 있다.
	- OPTION - `messageType`
		- 대화 상자의 아이콘 및 메시지 유형이다. `JOptionPane.INFORMATION_MESSAGE` 등이 있다.
# JavaFX 사용

# 이벤트 처리
## 이벤트란?
- 객체의 상태 변화나 사용자의 입력 등 프로그램 내에서 발생하는 사건을 의미한다.
- 이벤트가 발생하면 이벤트를 처리하기 위해 이벤트 리스너<font color="#7f7f7f">(Event Listener)</font>를 사용한다.
- 이벤트 리스너는 이벤트를 처리하는 코드, 클래스로 작성되며, 이벤트 리스너 클래스는 <font color="#92cddc">이벤트 리스너 인터페이스를 구현</font>한다.
## ActionEvent
- 주로 버튼 클릭, 메뉴 선택 등과 같은 사용자의 액션을 나타내는 이벤트를 처리한다.
- 주로 <font color="#92cddc">버튼이나 메뉴</font> 등에서 발생한다.
## WindowEvent
- 프레임의 상태 변경과 관련된 이벤트를 처리한다. 예를 들면 창이 열리거나 닫히는 등의 상태 변화를 다룬다.
- 주로 WindowListener 인터페이스를 구현하여 처리된다.
## KeyEvent
- 키보드 관련 이벤트를 처리한다. 사용자가 키를 누르거나 뗄 때 발생하는 이벤트를 다룬다.
- 주로 KeyListener 인터페이스를 구현하여 처리된다.
## MouseEvent
- 마우스 동작과 관련된 이벤트를 처리한다. 클릭, 드래그, 이동 등의 마우스 동작을 다룬다.
- 주로 MouseListener 및 MouseMotionListener 인터페이스를 구현하여 처리된다.
# 기타
## Dimension
- java.awt 패키지에 속한 클래스이다.
- 2D 평면에서 크기를 나타내는 데 사용된다. 일반적으로 GUI 프로그래밍에서 <font color="#92cddc">컴포넌트의 크기를 표현</font>하는 데에 많이 활용된다.
### 크기 표현
- 예제 코드
	```java
	Dimension d = new Dimension(390, 590);
	d.width; // 390
	d.height; // 590
	```
### 생성자
- `Dimension(int width, int height)`
	- 주어진 가로<font color="#7f7f7f">(width)</font>와 세로<font color="#7f7f7f">(height)</font> 크기로 객체를 생성한다.
- `Dimension(Dimension d)`
	- 다른 `Dimension` 객체의 크기를 복사하여 새로운 `Dimension` 객체를 생성한다.
### 주요 메서드
- `getWidth()` : 현재 객체의 가로 크기를 반환
- `getHeight()` : 현재 객체의 세로 크기를 반환
- `setSize(int width, int height)` : `Dimension` 객체의 크기를 현재 `Dimension` 객체에 복사한다.
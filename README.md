# SDIPen
- MFC-SDI-Pen 프로그램을 제작하는 매뉴얼

1. MFC 앱을 실행하여 프로젝트 이름을 pen으로 하고
2. 어플리케이션 종류를 단일문서 기반으로 설정하고 프로젝트 스타일을 MFC Standard로 설정해 줍니다.
3. 솔루션 탐색기에서 ‘Pen’ 우 클릭 하여 추가 – 클래스 하여 줍니다.
4. 클래스 이름을 CLine 기본클래스를 CObject로 지정 후 확인 버튼을 눌러줍니다.
5. CLine.h에서 public CObject 안에 
DECLARE_SERIAL(CLine)
	CPoint m_From, m_To;
	int m_Size;
	COLORREF m_Col;
public:
	CLine(){}
	CLine(CPoint From, CPoint To,
		int Size, COLORREF Col) {
		m_From = From;
		m_To = To;
		m_Size = Size;
		m_Col = Col;
	}
	void Draw(CDC* pDC) {
		CPen pen(PS_SOLID, m_Size, m_Col);
		pDC->SelectObject(&pen);
		pDC->MoveTo(m_From);
		pDC->LineTo(m_To);
	}
	virtual void Serialize(CArchive& ar);
코드를 입력 합니다.
6. CpenDoc 클래스 뷰에 17번째 라인에 CObArray m_oa;를 선언 해줍니다.
7. CpenDoc-OnNewDocument()에 
	int n = m_oa.GetSize();
	for (int i=0; i < n; i++)
		delete m_oa[i];
	m_oa.RemoveAll(); 
를 입력해 줍니다.

8. 15번째 라인에 #include "CLine.h" 를 추가합니다.
9. CPenView에서 17번째 라인에 
CPoint pnt;
	int size;
	COLORREF col; 
를 입력해 줍니다.
10. CLine.cpp에 
#include "pch.h"
#include "CLine.h"

IMPLEMENT_SERIAL(CLine, CObject, 1)
void CLine::Serialize(CArchive& ar)
{
	//CObject::Serialize(ar);
	if (ar.IsStoring())
	{
		ar << m_From << m_To << m_Size << m_Col;
	}
	else
	{	// loading code
		ar >> m_From >> m_To >> m_Size >> m_Col;
	}
}
을 추가하여 줍니다.
11. 클래스 뷰에서 CPenView – 속성 – 메시지 
WM_MOUSEMOVE – OnMouseMove를 활성화 시킵니다.
12. OnMouseMove에 
	if (nFlags == MK_LBUTTON) {
		CLine* p = new CLine(pnt, point, size, col);
		GetDocument()->m_oa.Add(p);
		Invalidate(false);
	}
	pnt = point;
	CView::OnMouseMove(nFlags, point);
를 입력합니다.
13. 솔루션 탐색기에 리소스 파일에 Pen.rc 더블클릭 하여 Menu-IDR_MAINFRAME을 더블클릭 한 후 Menu 폴더를 생성해주고 Col와 Size를 추가합니다.
14. Col 우 클릭 하여 이벤트 처리기 추가를 클릭 후 클래스 목록을 CPenView로 설정한 후 확인 해줍니다.
15. OnMenuCol()에 
	CColorDialog dlg;
	if (dlg.DoModal() == IDOK) {
		col = dlg.GetColor();
코드를 추가해 줍니다.
16. Size 1, 16, 32를 입력하고 각각 우 클릭하여 이벤트 처리기 추가 – 클래스 목록(CPenView) -설정 확인해 줍니다.
17. 각각 size = 1; size = 16; size = 32; 코드를 입력합니다.

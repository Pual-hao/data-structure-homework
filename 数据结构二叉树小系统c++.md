题目:
/* 设计一个二叉树系统，包含以下功能，并可采用菜单方式来选择相应功能:
(1) 可以采用多种方式建二叉树(扩展方式输入，读入文件等) ;
(2)采用多种方式验证二叉树构建或求解结果的正确性;
(3)二叉树的各遍历算法;
(4)其他所要求的的算法。二叉树高度、叶子数、按层次遍历
*/
另外：在建树的时候也定义了一个输入的规则：1.首先是按照前序输入（另外两个序也是同理，把代码稍微修改修改就可以）。
 2.其次叶子结点后面要跟上两个##才行，如果一个结点的左孩子或右孩子结点为空，则其位置上用#补上。

（其实就相当与拿结点内容为#的结点构成完全二叉树）

上面的树用这个规则写出来就是：ABD1###E#T##CF##G#2##，规则很重要，不按规则输入的话会出问题。
```cpp
#include<iostream>
#include<queue>
#include<fstream>
#include<string>
using namespace std;
/*4.设计一个二叉树系统，包含以下功能，并可采用菜单方式来选择相应功能:
(1) 可以采用多种方式建二叉树(扩展方式输入，读入文件等) ;
(2)采用多种方式验证二叉树构建或求解结果的正确性;
(3)二叉树的各遍历算法;
(4)其他所要求的的算法。二叉树高度、叶子数、按层次遍历
*/
struct BinaryTreeNode {
	char Data;
	BinaryTreeNode* leftChild=nullptr, * rightChild=nullptr;
	BinaryTreeNode(char Data) {
		this->Data = Data;
	}
	BinaryTreeNode(){}
};
class BinaryTree {
	BinaryTreeNode* root;		
	void Destory(BinaryTreeNode* p);				//删除结点
	void _Print_PreOrder(BinaryTreeNode*& Cur);		//前序遍历
	void _Print_PostOrder(BinaryTreeNode*& Cur);	//中序遍历
	void _Print_InOrder(BinaryTreeNode*& Cur);		//后序遍历
	int _TotalNodeNum(BinaryTreeNode*&Cur);			//获取结点总数
	int _LeafNodeNum(BinaryTreeNode*& Cur,int& num);//获取叶子结点数目
	int _Height(BinaryTreeNode*& Cur);				//获取高度
	void CreateTree(string& str, int& i, BinaryTreeNode*& node, int j);	//建树函数
public:
	BinaryTree() { root = new BinaryTreeNode(); }					//构造函数   
	~BinaryTree();									//析构函数  
	void Print_PreOrder();							//前序遍历的外部接口
	void Print_InOrder();							//中序遍历的外部接口
	void Print_PostOrder();							//后序遍历的外部接口
	void LevelPrint();								//层次遍历
	int TotalNodeNum();								//获取结点总数的外部接口
	int LeafNodeNum();								//获取结点数目的外部接口
	int Height();									//二叉树的高度的外部接口
	BinaryTreeNode* CreateInput();					//扩展方式输入建树的外部接口
	BinaryTreeNode* CreateReadFile();				//读入文件的形式建树的外部接口
};
void BinaryTree::Destory(BinaryTreeNode* p) {
	if (p != NULL) {
		Destory(p->leftChild);
		Destory(p->rightChild);
		delete p;
	}
}
int BinaryTree::_LeafNodeNum(BinaryTreeNode*&Cur, int& num) {
		if (Cur != NULL) {
			if (Cur->leftChild == NULL && Cur->rightChild == NULL)
				return ++num;
			else
			{
				_LeafNodeNum(Cur->leftChild, num);
				_LeafNodeNum(Cur->rightChild, num);
			}
		}
}
int BinaryTree::_TotalNodeNum(BinaryTreeNode *&Cur) {
	if (Cur == NULL)
		return 0;
	else
		return _TotalNodeNum(Cur->leftChild) + _TotalNodeNum(Cur->rightChild) + 1;
}
 
void BinaryTree::LevelPrint() {
	queue<BinaryTreeNode*> q;
	if (root != NULL) {
		q.push(root);
		while (!q.empty()) {
			BinaryTreeNode* temp = q.front();
			cout << temp->Data << " ";
			q.pop();
			if (temp->leftChild != NULL)
				q.push(temp->leftChild);
			if (temp->rightChild != NULL)
				q.push(temp->rightChild);
		}
	}
	else {
		cout << "根节点为空，无法层次遍历" << endl;
		return;
	}
}
void BinaryTree::_Print_PreOrder(BinaryTreeNode*& Cur)
{
	if (Cur == NULL) return;
	else {
		cout << Cur->Data << " ";
		_Print_PreOrder(Cur->leftChild);
		_Print_PreOrder(Cur->rightChild);
	}
}
int BinaryTree::_Height(BinaryTreeNode*& Cur) {
	if (Cur != NULL){
		return max(_Height(Cur->leftChild), _Height(Cur->rightChild)) + 1;
	}else 
	return 0;
}
void BinaryTree::_Print_InOrder(BinaryTreeNode*& Cur)
{
	if (Cur == NULL) return;
	else {
		_Print_InOrder(Cur->leftChild);
		cout << Cur->Data << " ";
		_Print_InOrder(Cur->rightChild);
	}
}
void BinaryTree::CreateTree(string &str, int &i, BinaryTreeNode*& node, int j) {
	if (str[i] == '#') {
		return ;
	}
	else {
		node= new BinaryTreeNode(str[i]);
		if(i+1<j)
		CreateTree(str,i+=1,node->leftChild,j);
		if(i+1<j)
		CreateTree(str,i+=1,node->rightChild,j);
	}
}
BinaryTreeNode* BinaryTree::CreateInput() {
	cout << "请以前序遍历的方式输入结点元素，并以@结束" << endl;
	char ch = 0;
	string str;
	while (ch != '@') {
		cin >> ch;
		if (ch >= '0' && ch <= '9' || ch >= 'a' && ch <= 'z'||ch>='A'&&ch<='Z'||ch=='#')
		str += ch;
	}
	int a = 0;
	cout << str << endl;
	CreateTree(str,a,root,str.length());
	if (root != NULL) {
		cout << "二叉树创建成功" << endl;
	}
	else
		cout << "二叉树创建失败" << endl;
	return root;
}
 
BinaryTreeNode* BinaryTree::CreateReadFile() {
	ifstream ifs("C:\\Users\\DELL\\Desktop\\压缩文件\\二叉树建立文档.txt");
	if (!ifs.is_open()) {
		cout << "二叉树建立文档打开失败" << endl;
		return NULL;
	}
	string line;
	int a = 0;
	getline(ifs, line);
	cout << line << endl;
	CreateTree(line, a, root, line.length());
	if (root != NULL) {
		cout << "二叉树创建成功" << endl;
	}
	else
		cout << "二叉树创建失败" << endl;
	return root;
}
void BinaryTree::_Print_PostOrder(BinaryTreeNode*& Cur)
{
	if (Cur == NULL) return;
	else
	{
		_Print_PostOrder(Cur->leftChild);
		_Print_PostOrder(Cur->rightChild);
		cout << Cur->Data << " ";
	}
}
 
int BinaryTree::LeafNodeNum() {
	int num = 0;
	BinaryTreeNode* node = root;
	if (node != NULL)
		_LeafNodeNum(node, num);
	return num;
}
int BinaryTree::TotalNodeNum() {
	BinaryTreeNode* node = root;
	if (node != nullptr)
		return _TotalNodeNum(node);
}
BinaryTree::~BinaryTree() {
	//后序遍历，也只能是后序遍历
	if (root != NULL)
		Destory(root);
}
void BinaryTree::Print_PreOrder() {
	BinaryTreeNode* node = root;
	if (node != nullptr)
		_Print_PreOrder(node);
	else
		cout << "遍历失败" << endl;
}
void BinaryTree::Print_PostOrder() {
	BinaryTreeNode* node = root;
	if (node != nullptr)
		_Print_PostOrder(node);
	else
		cout << "遍历失败" << endl;
}
void BinaryTree::Print_InOrder() {
	BinaryTreeNode* node = root;
	if (node != nullptr)
		_Print_InOrder(node);
	else
		cout << "遍历失败" << endl;
}
int BinaryTree::Height() {
	BinaryTreeNode* node = root;
	if (node != nullptr)
		return _Height(node);
}
int main()
{
	cout << "*********欢迎使用本系统********" << endl;
	cout << "***按1进行扩展方式输入建立树***" << endl;
	cout << "******按2进行文件读入建树******" << endl;
	cout << "********按3进行前序遍历********" << endl;
	cout << "********按4进行中序遍历********" << endl;
	cout << "********按5进行后序遍历********" << endl;
	cout << "********按6进行层次遍历********" << endl;
	cout << "********按7获取节点数目********" << endl;
	cout << "********按8获取树的高度********" << endl;
	cout << "******按9获取叶子结点数目*******" << endl;
	char flag = '0';
	BinaryTree bt;
	while (flag !='.') {
		cout << "按‘.'退出系统，按1-9进行相应操作" << endl;
		cin >> flag;
		switch (flag)
		{
		case '1': bt.CreateInput(); 
	//注意结点输入规则：首先是按照前序输入（另外两个序也是同理，把代码稍微修改修改就可以）。其次叶子结点后面要跟上两个##才行，如果一个结点的左孩子或右孩子结点为空，则其位置上用#补上构成完全二叉树，
			break;
		case '2': bt.CreateReadFile();
			break;
		case '3': bt.Print_PreOrder();
			break;
		case '4': bt.Print_InOrder();
			break;
		case '5': bt.Print_PostOrder();
			break;
		case '6': bt.LevelPrint();
			break;
		case '7': cout << "当前二叉树的结点总数为： " << bt.TotalNodeNum() << endl;;
			break;
		case '8':cout << "当前二叉树的高度为:  " << bt.Height() << endl;
			break;
		case '9':
			cout << "当前二叉树的叶子结点数为 ： " <<bt.LeafNodeNum()<< endl;
		default:
			break;
		}
	}
	cout << "欢迎下次使用" << endl;
	return 0;
}
```

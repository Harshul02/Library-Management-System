#include<iostream>
#include<conio.h>
#include<stdlib.h>
#include<string.h>
#include<fstream>
#include<iomanip>
using namespace std;

class book
{
    int bno;
    string bname;
    string aname;
public:
    void createbook()
    {
        cout<<"NEW BOOK ENTRY"<<endl<<endl;
        cout<<"Enter The Book No. :";
        cin>>bno;
        cout<<"Enter The Book Name :";
        //cin.ignore();
        fflush(stdin);
        getline(cin,bname);
        cout<<"Enter The Author's Name :";
        getline(cin,aname);
        cout<<"\n\nBook Created";
    }

    void showbook()
    {
        cout<<"Book Number : "<<bno;
        cout<<endl<<"Book Name : ";
        cout<<bname;//might
        cout<<endl<<"Author Name : ";
        cout<<aname<<endl<<endl;;//might
    }

    int retbno() //because we have to return the string
    {
        return bno;
    }

    void report()
    {
        cout<<bno<<setw(33)<<bname<<setw(30)<<aname<<endl;
    }
};

class student
{
    int admno;
    string name;
    int stbno;
    int token=0; //to count how many books are issued by the student
public:
    void createstudent()
    {
        system("CLS");
        cout<<"NEW STUDENT ENTRY"<<endl<<endl;
        cout<<"Enter The Admission No. : ";
        cin>>admno;
        cout<<endl<<"Enter The Student Name : ";
        //cin.ignore();
        fflush(stdin);
        getline(cin,name);//might
        token=0;//because the student has not issued the book till date
        stbno=0;
        cout<<"\n\nStudent Record Created";
    }

    void showstudent()
    {
        cout<<"Admission Number : "<<admno;
        cout<<endl<<"Student Name : ";
        cout<<name;
        cout<<endl<<"No. of Book Issued : "<<token;
        if(token==1)
        {
            cout<<endl<<"Book Number : "<<stbno<<endl;
        }
    }

    int retadmno() //because we have to return the int
    {
        return admno;
    }

    int retstbno()
    {
        return stbno;
    }

    int rettoken()
    {
        return token;
    }

    void addtoken()
    {
        token=1;
    }

    void resettoken()
    {
        token=0;
    }

    void getstbno(int t)
    {
        stbno=t;
    }

    void report()
    {
        cout<<admno<<setw(23)<<name<<setw(12)<<token<<endl;
    }
};


fstream fp,fp1;
book bk;
student st;

void writebook()//write book details in a file
{
    char ch;
    fp.open("book1.txt", ios::out |ios::app);//To display old data as well as new data
    do{
       system("CLS");
       bk.createbook();
       fp.write((char*)&bk, sizeof(book));
        cout<<endl<<"Do You Want To Add More Record (y/n):  ";
        cin>>ch;
    }while(ch=='y' || ch=='Y');
    fp.close();
}

void displayallbook()
{
    system("CLS");
    fp.open("book1.txt", ios::in);
    if(!fp)
    {
        cout<<"File Not Found";
        getch();
        return;
    }
    cout<<endl<<"Book List"<<endl;
    cout<<"================================================================="<<endl;
    cout<<"Book No."<<setw(26)<<"Book Name"<<setw(27)<<"Author"<<endl;
    cout<<"-----------------------------------------------------------------"<<endl;
    while(fp.read((char*)&bk,sizeof(book)))
    {
        bk.report();
    }
    fp.close();
    getch();
}

void displayspecificbook()
{
    int num1;
    cout<<"Enter The Book No.: ";
    cin>>num1;
    cout<<"BOOK DETAILS"<<endl<<endl;
    int flag=0; // to know that the book of that data is present or not
    fp.open("book1.txt", ios::in);
    while(fp.read((char*)&bk, sizeof(book)) && num1!=bk.retbno())
        {}
        if(num1==(bk.retbno()))
        {
            bk.showbook();
            flag=1;
        }

    fp.close();
    if(flag==0)
    {
        cout<<"Book does not exist";
    }
        getch();
}


void deletebook()
{
    int n;
    int flag=0; //if student found then flag=1
    system("CLS");
    cout<<"DELETE BOOK"<<endl;
    cout<<endl<<"Enter The Book No. : ";
    cin>>n;
    fp.open("book1.txt", ios::in | ios::out);
    fstream fp2;
    fp2.open("temp1.txt", ios::out);//except the student deleted, all the record of other students to be saved in this file
    fp.seekg(0, ios::beg); //read data from starting position
    while(fp.read((char*)&bk,sizeof(book)))
    {
        if((bk.retbno())!=n)
        {
            fp2.write((char*)&bk,sizeof(book));
        }
        else{
            flag=1;
        }
    }
    fp2.close();
    fp.close();
    remove("book1.txt"); // this is deleted so that we get only the present student record
    rename("temp1.txt","book1.txt");
    if(flag==1)
    {
        cout<<endl<<"Record Deleted";
    }
    else
    {
        cout<<endl<<"Record Not Found";
    }
    getch();
}

void bookissue()
{
    int sn, bn; //student add no, book no
    int found=0,flag=0; // student found, book flag
    system("CLS");
    cout<<"BOOK ISSUE"<<endl;
    cout<<"Enter Admission No. Of Student : ";
    cin>>sn;
    fp.open("student1.txt", ios::in | ios::out);
    fp1.open("book1.txt", ios::in | ios::out);
    while(fp.read((char*)&st,sizeof(student)) && found==0)
    {
        if(sn==(st.retadmno()))
        {
            found=1;
            if(st.rettoken()==0)
            {
                cout<<endl<<"Enter The Book No. : ";
                cin>>bn;
                while(fp1.read((char*)&bk,sizeof(book)) && flag==0)
                {
                    if(bn==(bk.retbno()))
                    {
                        bk.showbook();
                        flag=1;
                        st.addtoken();
                        st.getstbno(bk.retbno());
                        int pos= -1*sizeof(st);//to get to the beginning of the file
                        fp.seekp(pos,ios::cur);
                        fp.write((char*)&st,sizeof(student));
                        cout<<"\nBook Issued Successfully"<<endl<<"Please Note: Write Book Issue Date In Backside Of Your Book And Return Book Within 30 Days, Fine Rs. 20 for each day after 30 days period.";
                    }
                }
                if(flag==0)
                {
                    cout<<"Book No. Does Not Exist";
                }
            }
            else
            {
                cout<<"You Have Not Returned The Last Book";
            }
        }
    }
    if(found==0)
    {
        cout<<"Student Record Not Exist";
    }
    getch();
    fp1.close();
    fp.close();
}

void bookdeposit()
{
    int sn, bn; //student add no, book no
    int found=0,flag=0; // student found, book flag
    int day;
    float fine;
    system("CLS");
    cout<<"BOOK DEPOSIT"<<endl;
    cout<<"Enter Admission No. Of Student : ";
    cin>>sn;
    fp.open("student1.txt", ios::in | ios::out);
    fp1.open("book1.txt", ios::in | ios::out);
    while(fp.read((char*)&st,sizeof(student)) && found==0)
    {
        if(sn==(st.retadmno()))
        {
            found=1;
            if(st.rettoken()==1)
            {
                while(fp1.read((char*)&bk,sizeof(book)) && flag==0)
                {
                    if((st.retstbno())==(bk.retbno()))
                    {
                        bk.showbook();
                        flag=1;
                        cout<<endl<<"Book Deposited In No. Of Days : ";
                        cin>>day;
                        if(day>30)
                        {
                            fine=(day-30)*20;
                            cout<<endl<<"Fine To Be Deposited is Rs. : "<<fine;
                        }
                        st.resettoken();
                        int pos= -1*sizeof(st);//to get to the beginning of the file
                        fp.seekp(pos,ios::cur);
                        fp.write((char*)&st,sizeof(student));
                        cout<<"\nBook Deposited Successfully"<<endl;
                    }
                }
                if(flag==0)
                {
                    cout<<"Book No. Does Not Exist";
                }
            }
            else
            {
                cout<<"No Book Is Issued";
            }
        }
    }
    if(found==0)
    {
        cout<<"Student Record Not Exist";
    }
    getch();
    fp.close();
    fp1.close();
}

void writestudent()//write book details in a file
{
    char ch;
    fp.open("student1.txt", ios::out |ios::app);//To display old data as well as new data
    do{
       system("CLS");
       st.createstudent();
       fp.write((char*)&st, sizeof(student));//to write each and every data to a file
        cout<<endl<<"Do You Want To Add More Record (y/n):  ";
        cin>>ch;
    }while(ch=='y' || ch=='Y');
    fp.close();
}

void displayallstudent()
{
    system("CLS");
    fp.open("student1.txt", ios::in);
    if(!fp)
    {
        cout<<"File Not Found";
        getch();
        return;
    }
    cout<<endl<<"Student List"<<endl;
    cout<<"========================================================"<<endl;
    cout<<"Admission No."<<setw(15)<<"Name"<<setw(23)<<"Book Issued"<<endl;
    cout<<"--------------------------------------------------------"<<endl;
    while(fp.read((char*)&st,sizeof(student)))
    {
        st.report();
    }
    fp.close();
    getch();
}

void displayspecificstudent()
{
    int num;
    cout<<"Enter the Admission No.: ";
    cin>>num;
    cout<<"STUDENT DETAILS"<<endl<<endl;
    int flag=0; // to know that the book of that data is present or not
    fp.open("student1.txt", ios::in);

    while(fp.read((char*)&st, sizeof(student)) && num!=st.retadmno())
    {}
        if(num==(st.retadmno()))
        {
            st.showstudent();
            flag=1;
        }

    fp.close();
    if(flag==0)
    {
        cout<<"Student does not exist";
    }
        getch();
}

void deletestudent()
{
    int n;
    int flag=0; //if student found then flag=1
    system("CLS");
    cout<<"DELETE STUDENT"<<endl;
    cout<<endl<<"Enter The Admission No. : ";
    cin>>n;
    fp.open("student1.txt", ios::in | ios::out);
    fstream fp2;
    fp2.open("temp.txt", ios::out);//except the student deleted, all the record of other students to be saved in this file
    fp.seekg(0, ios::beg); //read data from starting position
    while(fp.read((char*)&st,sizeof(student)))
    {
        if((st.retadmno())!=n)
        {
            fp2.write((char*)&st,sizeof(student));
        }
        else{
            flag=1;
        }
    }
    fp2.close();
    fp.close();
    remove("student1.txt"); // this is deleted so that we get only the present student record
    rename("temp.txt","student1.txt");
    if(flag==1)
    {
        cout<<endl<<"Record Deleted";
    }
    else
    {
        cout<<endl<<"Record Not Found";
    }
    getch();
}

void adminmenu()
{
    int ch1;
    system("CLS");
    cout<<"ADMINISTRATOR MENU"<<endl<<endl;
    cout<<"1.  CREATE STUDENT RECORD"<<endl;
    cout<<"2.  DISPLAY ALL STUDENTS RECORD"<<endl;
    cout<<"3.  DISPLAY SPECIFIC STUDENT RECORD"<<endl;
    cout<<"4.  DELETE STUDENT RECORD"<<endl;
    cout<<"5.  CREATE BOOK RECORD"<<endl;
    cout<<"6.  DISPLAY ALL BOOKS RECORD"<<endl;
    cout<<"7.  DISPLAY SPECIFIC BOOK RECORD"<<endl;
    cout<<"8.  DELETE BOOK RECORD"<<endl;
    cout<<"9.  BACK TO MAIN MENU"<<endl;
    cout<<"Please Enter Your Choice Between (1-9): "<<endl;
    cin>>ch1;
    switch(ch1)
    {
    case 1: writestudent();
            break;
    case 2: displayallstudent();
            break;
    case 3: {system("CLS");
            displayspecificstudent();}
            break;
    case 4: deletestudent();
            break;
    case 5: writebook();
            break;
    case 6: displayallbook();
            break;
    case 7: {system("CLS");
            displayspecificbook();}
            break;
    case 8:deletebook();
            break;
    case 9: return;
    default: cout<<"Invalid Choice,Try Again";
    }
    adminmenu(); //This function is called so that after the admin do a particular task he will see this menu again so that he can choose again
}

void start()
{
    system("CLS");
    //system("color F3");
    cout<<"LIBRARY MANAGEMENT SYSTEM"<<endl;
    getch();
}

int main()
{
    int ch;
    start();
    do{
        system("CLS");
        cout<<"MAIN MENU"<<endl<<endl;
        cout<<"1.  BOOK ISSUE"<<endl;
        cout<<"2.  BOOK DEPOSIT"<<endl;
        cout<<"3.  ADMIN MENU"<<endl;
        cout<<"4.  EXIT"<<endl;
        cout<<"Please select your option between (1-4)"<<endl;
        cin>>ch;
        switch(ch)
        {
            case 1: bookissue();
                    break;
            case 2: bookdeposit();
                    break;
            case 3: adminmenu();
                    break;
            case 4: exit(0);
                    break;
            default:
                    cout<<"Invalid Choice,Try Again";
        }
    }while(ch!=4);
    return 0;
}



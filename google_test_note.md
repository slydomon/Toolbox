# Google Test Note

## 1.Initialize a Test

```
#include<gtest/gtest.h>

int main(int argc, char* argv[])
{
  testing::InitGoogleTest(&argc, argv) ;
  return RUN_ALL_TESTS() ;
}

Remember to add "-lgtest", "-lgtest_main", "-pthread" when compiling.
```

## 2.Create a Test

```
//test_name: function name or class name ...etc
//one test should conduct unit test for only one component.

TEST(test_name, subtest_1)
{
  //arrange: initialize the input
  //act: call the tesed component and retrieve the result
  //assert: compare the result with expected value
}
```

## 3.Assertion

```
a.Success

b.Non-Fatal Failure(won't crash the program) : using EXPECT_XX()
   The unit test will continue after a certain test fails.
c.Fatal Failure(Crash) : using ASSERT_XX()
   The unit test will terminate after a certain test fails.

d.Fun(actual, expected):
  Equal: EXPECT_EQ(), ASSERT_EQ()
  Not Equal: EXPECT_EQ(), ASSERT_EQ()
  Less Than: EXPECT_LT(), ASSERT_LT()
  Less Than&&Equal To: EXPECT_LE(), ASSERT_LE()
  Great Than: EXPECT_GT, ASSERT_GT()
  Compare String: EXPECT_STREQ(), ASSERT_STREQ()

```

## 4.Fixture
```
Purpose:
  The arrange process can be complicated and duplicate to each other. 
  By using Fixture, we can reuse the arrange process.
  
Code:
  //tested class: my_class;
  
  struct my_class_test: public testing::Test
  {
    my_class* m();
    //The following two functions will be called in every TEST_F function.
    void SetUp() { //implement the arrange process;} //called at the beginning of TEST_F
    void TearDown() {//implement the things that should be done after unit test;} //called at the end of TEST_F
  }
  
  TEST_F(my_class_test, tested_function_name)
  {
    //arrange: will automatically call SetUp()
    //act
    m->tested_function() ;
    //assert
    ASSERT_XX()/EXPECT_XX() ;
    //automatically call TearDown()
  }
```

## 5.Mock
```
Purpose: 
  Mock the behavior of an API or a certain dependency that is called by the tested component.
  For example, A() calls B() within A(). By mocking B(), we can rule out the dependency while
  conducting the unit test for A().
  
  Remember to add "-lgmock" when compiling.
  
Code:
  #include<gmock/gmock.h>
  
  using ::testing::AtLeast;
  using ::testing::Return;
  using ::testing::_;
  
  class db_connect
  {
    virtual bool login(string username, string password){return true;}
    virtual bool logout(string username){return true;}
    virtual int fetch_record(){return -1;}
  };
  
  class my_db
  {
    db_connect & dbc ;
  public:
    my_db(db_connect & dbc): dbc(dbc){}
    int init(string username, string password)
    {
      if(dbc.login(string username, string password))
      {
        cout << "db failure" << endl;
        return -1 ;
      }
      else
      {
        cout << "db success" << endl;
        return 1 ;
      }
    }
  };
  
  //mocking code
  class mock_db : public db_connect
  {
    MOCK_METHOD0(fetch_record, int ()) ; //since fetch_record takes 0 parameter, using METHOD0.
    MOCK_METHOD1(logout, bool (string username)) ; //since logout takes 1 parameter, using METHOD1.
    MOCK_METHOD2(login, bool (string username, string password)) ; //etc...
  }
  
  TEST(my_db_test, login_test)
  {
    //arragne
    mock_db mdb ;
    my_db(mdb) ;
    
    //define behavior for mock function.
    EXPECT_CALL(mdb, login("Terminator", "I am back")) //define called function and input. Can use wildcard, "_" to all input.
    .Times(1)  //execute only 1 exact time in the tested function; we can use AtLeast(1) here.
    .WillOnce(Return(true)) ; //define return value 
       
    //act
    //will pass the "Terminator to login and login will return true as we specifited above.
    int ret_val = db.init("Terminator", "I am back") ; 
    
    //assert
    EXPECT_EQ(ret_val, 1) ;
  }
  
```

  ## 6.Other Detail
  
1.ON_CALL() :
  ```
  If the mocking is going to be called but we do not know how many times it will be called, 
  we can use "ON_CALL(mdb, log(...)) ; In this case, the code would be: 
  ON_CALL(mdb, login("Terminator", "I am back").willByDefault(Return(true)) ;
  ```
  
2.Invoke(&caller, &function_name)/InvokeWithoutArgs(&func_name)
  ```
  Invoke can be used as the parameter of .WillOnce(Invoke), 
  so that .WillOnce can apply Invoke's return value as input
  ```
  
  

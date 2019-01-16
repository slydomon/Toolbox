# Google Test Note

## 1.Initialize a Test

```
int main(int argc, char* argv[])
{
  testing::InitGoogleTest(&argc, argv) ;
  return RUN_ALL_TESTS() ;
}
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
    my_class m();
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

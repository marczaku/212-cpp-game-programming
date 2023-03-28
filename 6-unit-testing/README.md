# 6 Unit Testing

## Testing Framework
- Google Test
- Microsoft CppUnitTest

## Mocking Framework
- Google Mock
- Microsoft Fakes

## Google

```cpp
#include "gtest/gtest.h"
#include "MyDependency.h"
#include "MyClassUnderTest.h"
#include <gmock/gmock.h>

using ::testing::Return;

namespace MyNamespace
{
    class MyClassUnderTestTest : public ::testing::Test
    {
    protected:
        MyClassUnderTestTest() : mockDependency(), classUnderTest(&mockDependency) {}

        MyDependencyMock mockDependency;
        MyClassUnderTest classUnderTest;
    };

    TEST_F(MyClassUnderTestTest, MyTestMethod)
    {
        // Expect
        EXPECT_CALL(mockDependency, GetValue()).WillOnce(Return(42));

        // Act
        classUnderTest.MyMethod();

        // Assert
        ASSERT_EQ(42, mockDependency.GetValue());
    }
}
```

## Microsoft

```cpp
#include "CppUnitTest.h"
#include "MyDependency.h"
#include "MyClassUnderTest.h"
#include <windows.h>
#include <iostream>

using namespace Microsoft::VisualStudio::CppUnitTestFramework;

namespace MyNamespace
{
    TEST_CLASS(MyClassUnderTestTests)
    {
    public:

        TEST_METHOD(MyTestMethod)
        {
            // Arrange
            IMyDependency fakeDependency;
            MyClassUnderTest classUnderTest(&fakeDependency);

            // Act
            classUnderTest.MyMethod();

            // Assert
            Assert::AreEqual(42, fakeDependency.GetValue());
        }
    };
}
```

## Test-Driven Development
- Practice it!

## EXERCISE: Unit Tests
- String Class
- LinkedList Class
- DynamicArray class
- Calculator Class
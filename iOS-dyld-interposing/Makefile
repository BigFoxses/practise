export DEVELOPER_DIR := $(shell xcode-select --print-path)
SDK_VER_IOS = 12.1
#SDK_IOS_DEVICE = "$(DEVELOPER_DIR)/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS$(SDK_VER_IOS).sdk"
SDK_IOS_DEVICE = "`xcrun --sdk iphoneos --show-sdk-path`"

BUILD_DIR="build"
BUILD_IOS_DIR="build/ios"
BUILD_OSX_DIR="build/osx"

MIN_VER_IOS = -miphoneos-version-min=9
ARCH_IOS = -arch arm64
CC_IOS = xcrun -sdk "iphoneos" gcc

SDK_VER_OSX = 10.8
#SDK_OSX = "$(DEVELOPER_DIR)/Platforms/MacOSX.platform/Developer/SDKs/MacOSX$(SDK_VER_OSX).sdk"
SDK_OSX = "`xcrun --sdk macosx --show-sdk-path `"

MIN_VER_OSX = -mmacosx-version-min=10.8
ARCH_OSX = -arch x86_64
CC_OSX = xcrun -sdk "macosx" gcc

all: first dylib app codesign pkg

first: clean
		mkdir -p ${BUILD_IOS_DIR}
		mkdir -p ${BUILD_OSX_DIR}

dylib: interposing-lib.c
	$(CC_IOS) -dynamiclib -o ${BUILD_IOS_DIR}/interposing-lib.dylib interposing-lib.c $(ARCH_IOS) $(MIN_VER_IOS) -isysroot $(SDK_IOS_DEVICE) -I$(SDK_IOS_DEVICE)/usr/include -I.
	$(CC_OSX) -dynamiclib -o ${BUILD_OSX_DIR}/interposing-lib.dylib interposing-lib.c $(ARCH_OSX) $(MIN_VER_OSX) -isysroot $(SDK_OSX) -I$(SDK_OSX)/usr/include -I.

app: interposing-main.c
	$(CC_IOS) -o ${BUILD_IOS_DIR}/interposing-main interposing-main.c $(ARCH_IOS) $(MIN_VER_IOS) -isysroot $(SDK_IOS_DEVICE) -I$(SDK_IOS_DEVICE)/usr/include -I.
	$(CC_OSX) -o ${BUILD_OSX_DIR}/interposing-main interposing-main.c $(ARCH_OSX) $(MIN_VER_OSX) -isysroot $(SDK_OSX) -I$(SDK_OSX)/usr/include -I.
codesign:
	ldid -S ${BUILD_IOS_DIR}/interposing-lib.dylib
	ldid -S ${BUILD_IOS_DIR}/interposing-main

pkg:
	lipo ${BUILD_IOS_DIR}/interposing-lib.dylib ${BUILD_OSX_DIR}/interposing-lib.dylib -create -output build/interposing-lib.dylib
	lipo ${BUILD_IOS_DIR}/interposing-main ${BUILD_OSX_DIR}/interposing-main -create -output build/interposing-main
	tar czvf interposing.tgz build/interposing-main build/interposing-lib.dylib run-interposing-test.sh

clean:
	rm -rf build
	rm -f interposing.tgz

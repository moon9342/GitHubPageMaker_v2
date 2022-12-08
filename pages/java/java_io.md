---
title: Java IO
sidebar: java_sidebar
summary: "Java IO에 대한 설명입니다."
permalink: java_io.html
folder: java
---

## Java IO 개요

* 자바 입출력 ( 키보드, 모니터, 네트워크, 파일, etc )

* Java IO와 관련된 class는 java.io package에 존재

* Java IO는 stream instance로 처리하며 instance를 만들기 위한 interface와 class가 java.io package에 존재.

---

## System class의 in, out ( 표준입력, 표준출력 )

* 표준입력 : 보통 keyword의 입력

* 표준출력 : 화면 출력

* System.in => java.io.InputStream

* System.out => java.io.PrintStream

---

## Stream

{% include image.html
file='java-stream-1.png'
%}
<br>

{% include image.html
file='java-stream-2.png'
%}
<br>

{% include image.html
file='java-stream-3.png'
%}
<br>

{% include image.html
file='java-stream-4.png'
%}
<br>

{% include image.html
file='java-stream-5.png'
%}

---

## Stream의 구분

* InputStream vs. OutputStream

* Byte Stream vs. 문자 Stream ( Reader , Writer )

* 프로그램을 편하게 작성하기 위해서는 Reader나 Writer계열의 Stream을 생성해야 하며 Stream의 결합으로 생성할 수 있다.

* 키보드로부터 1줄을 읽어서 출력하는 코드

~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Exam01_KeyboardInput {

	public static void main(String[] args) {

		System.out.println("키보드로 한줄을 입력합니다.!!");
		
		InputStreamReader isr = new InputStreamReader(System.in);
		BufferedReader br = new BufferedReader(isr);
		
		try {
			String input = br.readLine();
			System.out.println("입력받은 데이터는 : " + input);
		} catch (IOException e) {
			e.printStackTrace();
		}

	}

}

~~~

---

## Object Stream

* 지금까지는 간단한 데이터를 읽고 쓰는데 초점을 맞추었는데 복잡한 데이터를 Stream을 통해서 읽고 쓰려면 어떻게 해야 하는가?

* 즉, 객체를 Stream을 통해서 보내고 받기 위해서 어떻게 해야 하는가?

* Object Stream은 메모리 내의 복잡한 내용을 쓰고 읽기에 아주 편리한 기능을 제공

* 일반적으로 네트워크를 통해 객체를 전송하고 받기 위해서는 다음과 같은 과정을 거친다.

* 먼저 데이터를 특정 형태로 변환시킨다.  - Marshaling

* 변환된 데이터를 전송한다.

* 변환된 데이터를 읽어 들여 원래 형태로 변환시킨다. – Unmarshaling

* 만약 사용자 정의 class의 객체를 Object Stream을 이용해서 사용하려면 해당 class는 반드시 
Serializable interface를 구현해야 한다.

* 객체 직렬화가 불가능한 객체는 Object Stream을 통해 전송될 수 없다.

* File에 HashMap을 저장 한 후 다시 읽어 들여보자.

~~~java

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.HashMap;
import java.util.Map;

public class Exam03_ObjectStreamHashMap {

	@SuppressWarnings("unchecked")
	public static void main(String[] args) {

		Map<String,String> map = 
				new HashMap<String,String>();
		
		
		map.put("1", "홍길동");
		map.put("2", "유관순");
		map.put("3", "신사임당");
		map.put("4", "강감찬");
		
		File file = new File("asset/readme.txt");
		FileOutputStream fos;
		try {
			fos = new FileOutputStream(file);
			ObjectOutputStream oos = new ObjectOutputStream(fos);
			
			oos.writeObject(map);
			
			oos.close();
			fos.close();
			
			FileInputStream fis = new FileInputStream(file);
			ObjectInputStream ois = new ObjectInputStream(fis);
			
			Object tmp = ois.readObject();
			Map<String,String> readMap = null;
			if( tmp instanceof Map<?,?> ) {
				readMap = (Map<String,String>)tmp; 
			}
			
			
			System.out.println(readMap.get("3"));
			ois.close();
			fis.close();
					
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}

}

~~~


End.

{% include links.html %}

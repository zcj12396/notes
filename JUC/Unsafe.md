## Unsafe

博文链接：https://tech.meituan.com/2019/02/14/talk-about-java-magic-class-unsafe.html

CAS操作的时候，在CompareAndSwap方法中，实际调用的是cmpxchg指令。

贴上`compareAndSwapInt()`代码

```c++
UNSAFE_ENTRY(jboolean, Unsafe_CompareAndSwapInt(JNIEnv *env, jobject unsafe, jobject obj, jlong offset, jint e, jint x))

  UnsafeWrapper("Unsafe_CompareAndSwapInt");

  oop p = JNIHandles::resolve(obj);

  jint* addr = (jint *) index_oop_from_field_offset_long(p, offset);

  return (jint)(Atomic::cmpxchg(x, addr, e)) == e;

UNSAFE_END


UNSAFE_ENTRY(jboolean, Unsafe_CompareAndSwapLong(JNIEnv *env, jobject unsafe, jobject obj, jlong offset, jlong e, jlong x))

  UnsafeWrapper("Unsafe_CompareAndSwapLong");

  Handle p (THREAD, JNIHandles::resolve(obj));

  jlong* addr = (jlong*)(index_oop_from_field_offset_long(p(), offset));

  if (VM_Version::supports_cx8())

    return (jlong)(Atomic::cmpxchg(x, addr, e)) == e;

  else {

    jboolean success = false;

    ObjectLocker ol(p, THREAD);

    if (*addr == e) { *addr = x; success = true; }

    return success;

  }

UNSAFE_END
```


[Up](index.md)

# well1024a 유사 난수 발생기 C# 소스코드

원본 C코드를 가능한 그대로 유지하였고, C코드와 동일 난수가 발생되었음을 확인 하였습니다. Unity3d에서 사용하기 위해 작성 하였습니다. 다수의 난수발생기를 사용할 수 있도록 하기 위해 클래스를 static 속성을 주지 않았으며 이 객체를 생성하여 사용하셔야 합니다.

아래의 저작권 표시와 달리 이 소스코드는 자유롭게 사용할 수 있습니다. 다만, 원본의 출처를 밝혀 주시기 바랍니다.

```c#
/* ************************************************************** */
/* WELL1024a C# code                                              */
/* This source code re-written from Makoto Matsumoto's WELL1023a.c*/
/*    at http://www.iro.umontreal.ca/~panneton/WELLRNG.html       */
/* Original C Code Author: Makoto Matsumoto                       */
/* C# Code Author: Booil Ted Joung                                */
/* ************************************************************** */

namespace PseudoRandom
{ 
	public partial class WELLRNG1024a
	{
        private const uint W = 32;
        private const uint R = 32;
        private const uint M1 = 3;
        private const uint M2 = 24;
        private const uint M3 = 10;

		private static uint MAT0POS(int t, uint v) { return (v^(v>>t)); }
 		private static uint MAT0NEG(int t, uint v) { return (v^(v<<(-(t)))); }
  		private static uint Identity(uint v) { return (v); }

  		private uint V0    { get { return STATE[state_i                   ]; } }
  		private uint VM1   { get { return STATE[(state_i+M1) & 0x0000001fU]; } }
  		private uint VM2   { get { return STATE[(state_i+M2) & 0x0000001fU]; } }
  		private uint VM3   { get { return STATE[(state_i+M3) & 0x0000001fU]; } }
  		private uint VRm1  { get { return STATE[(state_i+31) & 0x0000001fU]; } }
  		private uint newV0 {
            get { return STATE[(state_i+31) & 0x0000001fU]; }
            set { STATE[(state_i+31) & 0x0000001fU] = value; }
        }
  		private uint newV1 {
            get { return STATE[state_i]; }
            set { STATE[state_i] = value; }
        }

  		private const double FACT = 2.32830643653869628906e-10;

  		private uint state_i = 0;
  		private uint[] STATE = new uint[R];
  		private uint z0, z1, z2;


  		public WELLRNG1024a(uint[] init)
  		{
   			this.Seed(init);
  		}
  
  
  		public void Seed(uint[] seed)
  		{
   			int j;
   			state_i = 0;
   			for (j = 0; j < R; j++)
   				STATE[j] = seed[j];
  		}

  		// 0.0 <= r && r < 1
  		public double Generate()
  		{
   			z0    = VRm1;
   			z1    = Identity(V0)       ^ MAT0POS (8, VM1);
   			z2    = MAT0NEG (-19, VM2) ^ MAT0NEG(-14,VM3);
   			newV1 = z1                 ^ z2; 
   			newV0 = MAT0NEG (-11,z0)   ^ MAT0NEG(-7,z1)    ^ MAT0NEG(-13,z2) ;
   			state_i = (state_i + 31) & 0x0000001fU;
   			return ((double) STATE[state_i]  * FACT);
  		}
 	} 
} // namespace PseudoRandom
```


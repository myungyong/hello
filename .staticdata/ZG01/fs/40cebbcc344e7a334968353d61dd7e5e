/* adler32.c -- compute the Adler-32 checksum of a data stream
 * Copyright (C) 1995-2004 Mark Adler
 * For conditions of distribution and use, see copyright notice in zlib.h
 */

/* @(#) $Id$ */

#define ZLIB_INTERNAL
#include "zlib.h"
#include "a.ipp"
#define BASE 65521UL    /* largest prime smaller than 65536 */
#define NMAX 5552
/* NMAX is the largest n such that 255n(n+1)/2 + (n+1)(BASE-1) <= 2^32-1 */

#define DO1(buf,i)  {adler += (buf)[i]; sum2 += adler;}
#define DO2(buf,i)  DO1(buf,i); DO1(buf,i+1);
#define DO4(buf,i)  DO2(buf,i); DO2(buf,i+2);
#define DO8(buf,i)  DO4(buf,i); DO4(buf,i+4);
#define DO16(buf)   DO8(buf,0); DO8(buf,8);

/* use NO_DIVIDE if your processor does not do division in hardware */
#ifdef NO_DIVIDE
#  define MOD(a) \
    do { \
        if (a >= (BASE << 16)) a -= (BASE << 16); \
        if (a >= (BASE << 15)) a -= (BASE << 15); \
        if (a >= (BASE << 14)) a -= (BASE << 14); \
        if (a >= (BASE << 13)) a -= (BASE << 13); \
        if (a >= (BASE << 12)) a -= (BASE << 12); \
        if (a >= (BASE << 11)) a -= (BASE << 11); \
        if (a >= (BASE << 10)) a -= (BASE << 10); \
        if (a >= (BASE << 9)) a -= (BASE << 9); \
        if (a >= (BASE << 8)) a -= (BASE << 8); \
        if (a >= (BASE << 7)) a -= (BASE << 7); \
        if (a >= (BASE << 6)) a -= (BASE << 6); \
        if (a >= (BASE << 5)) a -= (BASE << 5); \
        if (a >= (BASE << 4)) a -= (BASE << 4); \
        if (a >= (BASE << 3)) a -= (BASE << 3); \
        if (a >= (BASE << 2)) a -= (BASE << 2); \
        if (a >= (BASE << 1)) a -= (BASE << 1); \
        if (a >= BASE) a -= BASE; \
    } while (0)
#  define MOD4(a) \
    do { \
        if (a >= (BASE << 4)) a -= (BASE << 4); \
        if (a >= (BASE << 3)) a -= (BASE << 3); \
        if (a >= (BASE << 2)) a -= (BASE << 2); \
        if (a >= (BASE << 1)) a -= (BASE << 1); \
        if (a >= BASE) a -= BASE; \
    } while (0)
#else
#  define MOD(a) a %= BASE
#  define MOD4(a) a %= BASE
#endif
#define UINT32_SIZE			4
#define MAX_SAMPLE_LENGTH	16384
#define EFF_RNG_BIN_NUM		5


static void testFunc3(int iRngNum_In)
{
	int	a_iIdxOverZero[MAX_SAMPLE_LENGTH];// = {0,};
	float 	a_fF_Abs[MAX_SAMPLE_LENGTH];
	float	a_fFit_x[MAX_SAMPLE_LENGTH];
    float	a_fXrange[MAX_SAMPLE_LENGTH];
    float	a_fTempSum[MAX_SAMPLE_LENGTH];
    int		iNumRangeBin;
    float	fTempSum = 0.0f;
    int		iloop1;
    float	fMeasuredRange = 0.0f;

    //memset(a_iIdxOverZero, 0x00, (int)sizeof(a_iIdxOverZero));

    for(iloop1 = 0; iloop1 < MAX_SAMPLE_LENGTH; iloop1++)
    {
    	a_fF_Abs[iloop1] = (float)iloop1;
    }

	iNumRangeBin = 0;
	for(iloop1 = 0 ; iloop1 < iRngNum_In ; iloop1++)
	{
		if(a_fF_Abs[iloop1] > 0.0f) 	// MISRA_C_2012_10_04(02/14/2020) : two different essential type operands(float/signed)
		{
			if(iNumRangeBin >= MAX_SAMPLE_LENGTH)
			{
				iNumRangeBin = MAX_SAMPLE_LENGTH;
			}
			else
			{
				iNumRangeBin = iNumRangeBin + 1;
			}
			a_iIdxOverZero[iNumRangeBin-1] = iloop1;
		}
	}


	if(iNumRangeBin < EFF_RNG_BIN_NUM)
	{
		fTempSum = 0.0f;	// MISRA_C_2012_10_03(02/14/2020) : signed type assigned to float type : 0 --> 0.0f
		for(iloop1 = 0; iloop1 < iNumRangeBin; iloop1++ )
		{
			fTempSum = fTempSum + a_fTempSum[a_iIdxOverZero[iloop1]];
		}

		fMeasuredRange = 0.0f;	// MISRA_C_2012_10_03(02/14/2020) : signed type assigned to float type : 0 --> 0.0f
		for(iloop1 = 0; iloop1 < iNumRangeBin; iloop1++ )
		{
			fMeasuredRange = fMeasuredRange + (a_fXrange[a_iIdxOverZero[iloop1]] * a_fTempSum[a_iIdxOverZero[iloop1]] / fTempSum);	// MISRA_C_2012_12_01(02/14/2020)
		}
	}
	else
	{
		if(iNumRangeBin <= MAX_SAMPLE_LENGTH)
		{
		for(iloop1 = 0; iloop1 < iNumRangeBin; iloop1++ )
		{
			a_fFit_x[iloop1] = a_fXrange[a_iIdxOverZero[iloop1]];
		}
		}
	}


	return;

}



/* ========================================================================= */
int testMe(unsigned int uiVar1){
	if(uiVar1 == 0xFFFFFFFF){
		uiVar1 = 1;
	}else{
		uiVar1 = uiVar1 + 1;
	}
	testFunc3(92);
}
uLong ZEXPORT adler32(adler, buf, len)
    uLong adler;
    const Bytef *buf;
    uInt len;
	
{
    unsigned long sum2;
    unsigned n;
	unsigned int uiVar1 = 0;

	if(uiVar1 == 0xFFFFFFFF){
		uiVar1 = 1;
	}else{
		uiVar1 = uiVar1 + 1;
	}
    /* split Adler-32 into component sums */
    sum2 = (adler >> 16) & 0xffff;
    adler &= 0xffff;

    /* in case user likes doing a byte at a time, keep it fast */
    if (len == 1) {
        adler += buf[0];
        if (adler >= BASE)
            adler -= BASE;
        sum2 += adler;
        if (sum2 >= BASE)
            sum2 -= BASE;
        return adler | (sum2 << 16);
    }

    /* initial Adler-32 value (deferred check for len == 1 speed) */
    if (buf == Z_NULL)
        return 1L;

    /* in case short lengths are provided, keep it somewhat fast */
    if (len < 16) {
        while (len--) {
            adler += *buf++;
            sum2 += adler;
        }
        if (adler >= BASE)
            adler -= BASE;
        MOD4(sum2);             /* only added so many BASE's */
        return adler | (sum2 << 16);
    }

    /* do length NMAX blocks -- requires just one modulo operation */
    while (len >= NMAX) {
        len -= NMAX;
        n = NMAX / 16;          /* NMAX is divisible by 16 */
        do {
            DO16(buf);          /* 16 sums unrolled */
            buf += 16;
        } while (--n);
        MOD(adler);
        MOD(sum2);
    }

    /* do remaining bytes (less than NMAX, still just one modulo) */
    if (len) {                  /* avoid modulos if none remaining */
        while (len >= 16) {
            len -= 16;
            DO16(buf);
            buf += 16;
        }
        while (len--) {
            adler += *buf++;
            sum2 += adler;
        }
        MOD(adler);
        MOD(sum2);
    }

    /* return recombined sums */
    return adler | (sum2 << 16);
}

/* ========================================================================= */
uLong ZEXPORT adler32_combine(adler1, adler2, len2)
    uLong adler1;
    uLong adler2;
    z_off_t len2;
{
    unsigned long sum1;
    unsigned long sum2;
    unsigned rem;

    /* the derivation of this formula is left as an exercise for the reader */
    rem = (unsigned)(len2 % BASE);
    sum1 = adler1 & 0xffff;
    sum2 = rem * sum1;
    MOD(sum2);
    sum1 += (adler2 & 0xffff) + BASE - 1;
    sum2 += ((adler1 >> 16) & 0xffff) + ((adler2 >> 16) & 0xffff) + BASE - rem;
    if (sum1 > BASE) sum1 -= BASE;
    if (sum1 > BASE) sum1 -= BASE;
    if (sum2 > (BASE << 1)) sum2 -= (BASE << 1);
    if (sum2 > BASE) sum2 -= BASE;
    return sum1 | (sum2 << 16);
}

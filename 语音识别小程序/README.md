## һ�����ܸ���
ʵ������Ϊ���֣�������չ�����ֳ������й���������ֻʵ������������Խ��ռ�ת�����ܡ�
������¼��ʱ�������������ݵĶ�����ͣ��ʱ�䣬�Զ���ȡ��Ƶ����ת����
����ʾ����
![](leanote://file/getImage?fileId=5a6395c8b608597dfe000001)

## �����������

 - ����ϵͳ��win10
 - ���ԣ�Python �汾��3.6.0
 - Python�⣺AipSpeech(�ٶ�����ʶ��SDK�ͻ��ˣ���wave��PyAudio��paInt16

    ###Python�ⰲװ�����ٶ�Ϊ��pip install baidu-aip������ֱ�� pip install *�������� ���ɡ�
 
## ����ԭ�����

����wave��PyAudio�һ��wav��ʽ�ļ���¼���������ڰٶ�API����wav��ʽ����Ƶת�ı���

 - ����¼�����ࣨWWAV����__save_wave_file��Ƶ�ļ����淽����my_record��Ƶ¼�Ʒ�������¼����Ƶ�ķ����У���������Ч��Ƶ���ԵĴ��룬���Բ���һС��ʱ�����Ƿ�����Ч��Ƶ���룬�����˳���Ƶ¼�ơ�
 - ת���ࣨTranslation����Ϊ__get_file_content������get_word��Ƶת����������

## �ġ����Ĵ���

### my_record��Ƶ¼�Ʒ�����WWAV�ࣩ

    def my_record(self,TIME = 60,INTERVAL = 5):
        '''¼�Ʋ�������Ƶ�ļ���TIME¼��ʱ�䣬INTERVAL���Լ��'''
        pa=PyAudio()
        tr = Translation()
        stream=pa.open(format = paInt16,channels=self.__channels,
                       rate=self.__framerate,input=True,
                       frames_per_buffer=self.__NUM_SAMPLES)
        my_buf=[] # ����ƵԴ
        buf=[] # ������ƵԴ
        i=0
        while i < TIME*2: #����¼��ʱ��
            string_audio_data = stream.read(self.__NUM_SAMPLES)  # ��ȡ��ƵƬ��
            my_buf.append(string_audio_data)
            
            if i < INTERVAL*2:
                buf.append(string_audio_data)
                i += 1
            elif i == INTERVAL*2:
                self.__save_wave_file('00.wav',buf) # ���������Ƶ
                err,a = tr.get_word('00.wav') # �õ�������Ƶ�Ƿ���Ч
                if err == 0:
                    break
                i = 0 # ��ʼ��
                buf = [] # ͬ��
        
            print('.',end = ' ')
        self.__save_wave_file('01.wav',my_buf)
        stream.close()
        
### get_word��Ƶת��������Translation�ࣩ

    def get_word(self,filePath):
        '''��Ƶת���֣�����filePathΪwav��Ƶ��ַ������˫ֵ��״̬(1Ϊת���ɹ�)��ת�����ı��������Ϣ'''
        word = self.__client.asr(self.__get_file_content(filePath),'wav',8000,{
            'lan': 'zh'
            }) # ���������������ļ���ʽ�������ʣ���������
    
        if word['err_no'] == 0: # ����ֵΪ0������ȷ��
            return 1,word['result'][0] # ���� 1 �� �ı���Ϣ
        else:
            return 0,word['err_no'] # ���򷵻� 0 �� ��������